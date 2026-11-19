# Research: iOS SSH Apps and Libraries

_Step 1 of the plan. Compiled 2026-11-17. Repo stats come from the GitHub API on that date._

## TL;DR: recommended stack

| Layer | Pick | License | Why |
|---|---|---|---|
| UI | **SwiftUI** (UIKit where needed), iOS/iPadOS 17+ | — | One codebase for iPhone and iPad, with Stage Manager and external-keyboard support |
| SSH transport | **Citadel** on top of **SwiftNIO SSH** | MIT + Apache-2.0 | Pure Swift, async/await, PTY, SFTP, jump hosts. No C/OpenSSL build pain. App Store–friendly licenses |
| Terminal | **SwiftTerm** (`TerminalView` for UIKit) | MIT | The de facto Swift xterm emulator, very active, and used by shipping apps |
| Keys | **Secure Enclave P-256 keys** (CryptoKit) + Keychain, gated by Face ID (LocalAuthentication) | Apple | Private key never leaves the chip. `ecdsa-sha2-nistp256` is supported by SwiftNIO SSH and by every modern OpenSSH. Import ed25519/RSA keys into the Keychain as a fallback |
| Network reach | **Tailscale app** on iPhone/iPad + targets | BSD-3 (client) | Reach home and office machines from anywhere without opening ports. No in-app integration needed |
| Session persistence | **tmux on the server** + auto-reconnect in the app (v1). **Mosh** later (step 4) | — / GPL-3.0 | Covers about 90% of Mosh's value with zero licensing risk |

**Fallback:** if Citadel hits a blocker (an algorithm gap or a bug), drop down to raw SwiftNIO SSH channels, which Citadel already wraps. libssh2 is the last-resort option.

---

## 1. Existing iOS SSH apps (what to learn from)

| App | Model | Strengths | Weaknesses / gaps | Takeaway for us |
|---|---|---|---|---|
| **Blink Shell** | Subscription. Source is GPL-3.0 ([blinksh/blink](https://github.com/blinksh/blink), 7k★, active Nov 2026) | Best Mosh support on iOS, excellent keyboard handling, `ssh`-config style hosts, VS Code (code-server) integration | Power-user UX with a steep learning curve. GPL means we can't reuse its code in a closed App Store app | Copy its keyboard and Mosh ideas, not its code |
| **Termius** | Freemium plus subscription, cross-platform | Polished host manager, cloud sync, snippets, SFTP, port forwarding, team sharing | Expensive subscription, account required for sync, closed source | Model for host management and sync UX |
| **Prompt 3** (Panic) | Paid / subscription | Native polish, hardware-key and Secure Enclave auth, Mosh, Panic Sync | Fewer power features than Blink | Model for native feel and key handling |
| **Secure ShellFish** | Freemium | Exposes servers in the iOS **Files** app (File Provider), tmux integration, widgets | Terminal is secondary | Model for file access via File Provider extension (step 4) |
| **a-Shell** | Free, BSD-3 ([holzschu/a-shell](https://github.com/holzschu/a-shell)) | Local Unix shell on iOS, includes ssh | Not a host manager | Reference for ios_system tricks |
| **Rayon** | Open source ([Lakr233/Rayon](https://github.com/Lakr233/Rayon), archived 2022) | SwiftUI SSH machine manager | Archived, license unclear | Useful only as a UI reference |
| **SwiftTermApp** | MIT ([migueldeicaza/SwiftTermApp](https://github.com/migueldeicaza/SwiftTermApp), archived Dec 2025) | Reference SwiftUI + SwiftTerm + SSH app by SwiftTerm's author | Archived, and used libssh2 | Best code reference for wiring SwiftTerm into SwiftUI |

**What's missing across the market (our opportunity):** a simple, private, no-account app tuned to *our own* machines. That means one-tap connect to saved hosts over Tailscale, Face ID–protected Secure Enclave keys, iCloud sync without a vendor account, and good iPad keyboard and multi-window support, all without a subscription.

## 2. SSH libraries for Swift

| Library | Type | License | Last activity | Notes | Verdict |
|---|---|---|---|---|---|
| [apple/swift-nio-ssh](https://github.com/apple/swift-nio-ssh) | Pure-Swift SSH protocol | Apache-2.0 | v0.15.1, Oct 2026 (525★) | Apple-maintained. Low-level building blocks only (channels, auth). Algorithms: ed25519, ECDSA P-256/384/521, curve25519 KEX, AES-GCM. No built-in RSA, SFTP or agent | ✅ Foundation |
| [orlandos-nl/Citadel](https://github.com/orlandos-nl/Citadel) | High-level client/server on NIOSSH | MIT | v0.12.1 Apr 2026, commits Jun 2026 (388★) | async/await API: `executeCommand`, `withPTY`, SFTP client, jump hosts, adds RSA keys. Pre-1.0, so expect API churn | ✅ **Primary pick** |
| [gaetanzanella/swift-ssh-client](https://github.com/gaetanzanella/swift-ssh-client) | High-level on NIOSSH | MIT | Oct 2023 (26★) | Small and stale | ❌ |
| [libssh2/libssh2](https://github.com/libssh2/libssh2) | C library | BSD-3 | active, last release 1.11.1 (Oct 2024) | Battle-tested, broad algorithm support. Needs an OpenSSL/mbedTLS cross-build for iOS. Blocking C API | ⚠️ Fallback only |
| [NMSSH](https://github.com/NMSSH/NMSSH) | Obj-C wrapper of libssh2 | MIT | Mar 2024 | Effectively unmaintained | ❌ |
| [Shout](https://github.com/jakeheis/Shout) | Swift wrapper of libssh2 | MIT | Jun 2024 | macOS-oriented, quiet | ❌ |
| [SwiftSH](https://github.com/Frugghi/SwiftSH) | Swift wrapper of libssh2 | MIT | Nov 2021 | Abandoned | ❌ |
| [DimaRU/Libssh2Prebuild](https://github.com/DimaRU/Libssh2Prebuild) | Prebuilt libssh2 xcframework | MIT | Jan 2024 | Handy if we ever need libssh2 | ⚠️ Fallback helper |

**Why Citadel/NIOSSH over libssh2:** it uses Swift concurrency natively, has no OpenSSL to cross-compile, fits Secure Enclave keys directly (CryptoKit P-256 is the same type NIOSSH uses), and carries permissive licenses. The risks are that it's pre-1.0 and has a smaller community. We mitigate that by pinning versions and isolating it behind our own `SSHSession` protocol so the backend can be swapped.

## 3. Terminal rendering

| Library | License | Status | Notes | Verdict |
|---|---|---|---|---|
| [migueldeicaza/SwiftTerm](https://github.com/migueldeicaza/SwiftTerm) | MIT | v1.99.3 Nov 2026, commits daily (1.8k★) | xterm/VT100, UIKit and AppKit views, true color, mouse, Sixel/Kitty graphics, good Unicode. Ships `TerminalViewDelegate` for wiring bytes in and out. No SSH built in, which is by design | ✅ **Pick** |
| Blink's hterm (WKWebView) | GPL-3.0 / BSD | — | JS-based, heavier, GPL entanglement | ❌ |
| Write our own | — | — | Months of work for no gain | ❌ |

Integration pattern: SwiftTerm `TerminalView` (UIKit) wrapped in `UIViewRepresentable`. Keystrokes go through `send(source:data:)` to the Citadel PTY stdin writer, and PTY output goes to `feed(byteArray:)`. Window resizes are forwarded as SSH `window-change`.

## 4. Mosh (roaming, intermittent connectivity)

- [mobile-shell/mosh](https://github.com/mobile-shell/mosh): GPL-3.0 (with an OpenSSL exception), 14.7k★. The last release was **1.4.0 in Oct 2022** and commits are sparse, so it's stable but slow-moving. It's C++ and needs protobuf.
- No maintained pure-Swift Mosh client exists. Blink's Mosh integration is GPL.
- **Implications:** using Mosh means linking GPL code. That's fine for a personal sideloaded build. For an App Store release, the whole app would effectively need to be GPL-3.0, which can be done (Blink does it) but is a business decision.
- **Recommendation:** v1 uses **tmux + fast auto-reconnect** (re-attach `tmux new -As main` on reconnect). Revisit Mosh in step 4, either as a GPL build flag or by writing a clean-room Swift implementation of the State Synchronization Protocol (the protocol is documented, and that work is moderate).
- Alternative worth knowing: Eternal Terminal (`et`, Apache-2.0) also does reconnect but needs its own server daemon.

## 5. Supporting pieces

- **Tailscale** ([tailscale/tailscale](https://github.com/tailscale/tailscale), BSD-3, very active): install the official app on iPhone/iPad and each target, and SSH to MagicDNS names. Our app just sees a normal TCP route. Optional later: Tailscale SSH (auth via tailnet identity).
- **Keys:** CryptoKit `SecureEnclave.P256.Signing.PrivateKey` with `.biometryCurrentSet` access control (Face ID per use, or per session). Export the public key in OpenSSH format for `authorized_keys`.
- **Host-key verification:** TOFU (trust on first use) with a known_hosts store. Never use Citadel's `.acceptAnything()` outside tests.
- **Sync (step 4):** SwiftData/CloudKit for hosts and settings. Private keys stay device-bound (Secure Enclave keys can't sync, which is a feature).
- **File transfer (step 4):** Citadel SFTP plus an optional File Provider extension (the Secure ShellFish model).
- **Port forwarding (step 4):** NIOSSH `direct-tcpip` channels with a local listener. Note that iOS suspends background sockets, so forwarding works mainly while in the foreground.
- **iOS background limits:** apps get roughly 30 seconds after backgrounding. Plan for graceful disconnect and fast reconnect rather than "always connected".

## 6. Licensing summary for an eventual App Store release

All v1 picks (Citadel MIT, SwiftNIO/NIOSSH Apache-2.0, SwiftTerm MIT) are permissive, so they're fine for a closed-source App Store app with attribution in an Acknowledgements screen. **Only Mosh (GPL-3.0) changes that**, which is why it's isolated to step 4.

## Sources

- GitHub repository metadata (stars, license, last push, releases) via api.github.com, queried 2026-11-17
- [Citadel README](https://github.com/orlandos-nl/Citadel)
- [Introducing SwiftNIO SSH, swift.org](https://swift.org/blog/swiftnio-ssh/)
- [SwiftNIO SSH in the SSH implementation comparison](https://ssh-comparison.quendi.de/impls/swift-nio-ssh.html)
- [TermAI: best SSH client for iPhone](https://termai.sh/blog/best-ssh-client-iphone) (vendor-written, so read critically)
- [Cosyra: best terminal apps for iPhone](https://cosyra.com/guides/best-terminal-apps-iphone.html)
- [SourceForge: SSH clients for iPhone](https://sourceforge.net/software/ssh-clients/iphone/)

_App pricing and feature claims for commercial apps are from 2026 roundups and should be re-checked on the App Store before relying on them._
