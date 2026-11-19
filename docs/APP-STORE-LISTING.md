# OmniSSH: App Store listing and submission checklist

Everything to enter in App Store Connect for version 1.0, plus the steps only the account holder can do.

## App information

| Field | Value |
|---|---|
| Name | OmniSSH |
| Subtitle (30) | SSH terminal for all your Macs |
| Bundle ID | dev.mobilessh.MobileSSH (keeps existing installs, keys and hosts; see note below) |
| SKU | omnissh-001 |
| Platforms | iOS (iPhone and iPad) and macOS (Mac Catalyst build of the same app, universal purchase) |
| Primary category | Developer Tools |
| Secondary category | Utilities |
| Age rating | 4+ (answer "None" to every content question; no unrestricted web access) |
| Copyright | © 2026 Soo X Intelligence LLC. All rights reserved. |
| Privacy Policy URL | https://www.chengjianglong.com/omnissh/privacy/ |
| Terms of Use (EULA) | https://www.chengjianglong.com/omnissh/terms/ (or leave Apple's standard EULA selected) |
| Support URL | https://www.chengjianglong.com/ |
| Marketing URL | (optional) https://www.chengjianglong.com/ |
| Version / build | 1.0 (1) |

Bundle ID note: the archives use `dev.mobilessh.MobileSSH`, the ID the app has had since day one. Keeping it means the copy already on your iPhone, iPad and Mac updates in place and keeps its Face ID keys and saved machines. A new ID such as `com.chengjianglong.omnissh` would make every device treat it as a new app, so keys would have to be re-created and re-authorized on every server. The ID can't be changed once the App Store Connect record exists.

## Subscriptions (group: OmniSSH Pro)

| Reference name | Product ID | Duration | Price | Introductory offer |
|---|---|---|---|---|
| OmniSSH Pro Monthly | dev.mobilessh.pro.monthly | 1 month | $9.99 (Tier) | Free trial, 3 days |
| OmniSSH Pro Yearly | dev.mobilessh.pro.yearly | 1 year | $99.99 | Free trial, 3 days |

Display name: "OmniSSH Pro". Description: "Unlimited machines, tabs, panes, Web Ports, live status and screen control on iPhone, iPad and Mac."
Each subscription needs its own review screenshot: use `screenshots/iphone-6.9/4-settings.png` (shows the Subscription section) until a paywall screenshot is taken in Xcode with the StoreKit config.

## Promotional text (170)

Your Macs, Linux boxes and GPU servers in your pocket: Face ID keys, tmux sessions that survive, live CPU/GPU status, Web Ports and screen control.

## Description

OmniSSH is a fast, modern SSH terminal for iPhone, iPad and Mac, built for people who run real work on real machines.

KEYS THAT NEVER LEAVE YOUR DEVICE
Create SSH keys inside the Secure Enclave, unlocked with Face ID or Touch ID. Private keys can't be copied off your device, not even by OmniSSH.

SESSIONS THAT SURVIVE
OmniSSH keeps each machine's terminals in tmux and reconnects automatically when you switch networks or come back to the app, right where you left off.

A REAL TERMINAL
• Tabs and split panes, each a tmux window you can rename and rearrange
• Find in scrollback, snippets, triggers that highlight or alert on output
• Broadcast typing to several machines at once
• Swipe to scroll through history, a compact key row with esc, ctrl, tab and arrows
• Themes, font sizes and cursor styles per machine

EVERY MACHINE AT A GLANCE
See which machines are online and their live CPU, memory and GPU use before you even open a terminal.

WEB PORTS
Open Jupyter, TensorBoard or your dev server running on a remote machine, forwarded securely over SSH. No open ports, no VPN setup.

SCREEN CONTROL
See and drive your Mac's desktop from your iPhone or iPad over an encrypted connection.

REACH ANYWHERE
Works great with Tailscale: your Mac and servers are one tap away from anywhere, without exposing them to the internet.

VERIFIED AND PRIVATE
Every server's fingerprint is checked so nobody can impersonate it. OmniSSH has no accounts, no tracking and no analytics; it talks only to the machines you add.

OMNISSH PRO
OmniSSH Pro unlocks the app on iPhone, iPad and Mac with one subscription: $9.99 a month or $99.99 a year, each starting with a 3-day free trial. Payment is charged to your Apple Account. The subscription renews automatically unless cancelled at least 24 hours before the end of the current period, including the free trial. Manage or cancel anytime in your App Store account settings.

Terms of Use: https://www.chengjianglong.com/omnissh/terms/
Privacy Policy: https://www.chengjianglong.com/omnissh/privacy/

## Keywords (100)

ssh,terminal,tmux,server,shell,sftp,mosh,devops,linux,remote,console,tailscale,gpu,jupyter,vnc

(Remove "sftp" and "mosh" if you'd rather not list features that aren't built yet. Reviewers can flag keywords for missing features.)

## What's New (1.0)

First release.

## App Privacy ("nutrition label")

Data collection: **No, we do not collect data from this app.** (No analytics, crash reporting, accounts or ads; purchases are handled by Apple.)

## Export compliance

`ITSAppUsesNonExemptEncryption` is set to NO in Info.plist. OmniSSH uses only standard encryption (SSH, AES, the platform's TLS) for its core purpose, which falls under the exemption for standard cryptography in mass-market software. If App Store Connect asks, answer that the app uses encryption limited to standard algorithms and is exempt.

## App Review information

| Field | Value |
|---|---|
| Sign-in required | No (no accounts) |
| Contact | Chengjiang Long, chengjiang.long@gmail.com, phone: (you fill in) |
| Demo account / server | Not needed: the app has a built-in demo machine (a simulated terminal that runs on the device) |

Review notes (paste as-is):

> OmniSSH is an SSH terminal for connecting to the user's own computers and servers. No account or sign-in is needed.
>
> So you can review it without a server, the app includes a built-in demo machine that simulates a terminal entirely on the device:
> 1. On the home screen, under "Get started", tap "Try the demo machine".
> 2. A terminal opens. Type `help` and press return to see the commands; try `top`, `nvidia-smi`, `neofetch`, `seq 200` (then swipe down to scroll back), and the ↑ / ↓ keys.
> 3. Try the ⋯ menu (Find, Snippets, Appearance for themes and font size), the key row above the keyboard (esc, ctrl, tab, arrows, and the last key to hide or show the keyboard), and Settings (gear icon).
> 4. Back on the home screen, the demo machine shows live CPU, memory and GPU status, as a real server would.
>
> Connecting to real machines uses a key created on the device (key icon) whose public key the user adds to their server. Web Ports and screen control need a real server or Mac and are not available in the demo.
> OmniSSH Pro subscriptions can be tested with a Sandbox account; the plans screen appears on first launch. The demo machine can be removed by swiping left on it.

If Apple still asks to see a real connection, [DEMO-SERVER.md](DEMO-SERVER.md) explains how to set up a small server for reviewers.

## Screenshots

In `AppStore/screenshots/` of the Xcode project (copies of the main shots are in [screenshots/app-store](screenshots/app-store)):

| Folder | Size | Shots |
|---|---|---|
| iphone-6.9 | 1320 × 2868 | 1-home, 2-terminal, 3-tools, 4-settings |
| ipad-13 | 2064 × 2752 | 1-home |
| mac | 1440 × 900 | 1-home |

## Builds

Signed for the App Store and saved in Xcode's Organizer (Window → Organizer → Archives):

- `OmniSSH 1.0 (1) iOS`: iPhone and iPad
- `OmniSSH 1.0 (1) Mac`: Mac App Store

## Steps only you can do

1. **Agreements**: in App Store Connect → Business, accept the Paid Apps agreement and fill in banking and tax. Subscriptions can't go live without it.
2. **Create the app record**: App Store Connect → Apps → + → New App. Platforms iOS and macOS, name OmniSSH, bundle ID `dev.mobilessh.MobileSSH`, SKU `omnissh-001`.
3. **Create the two subscriptions** in a group named "OmniSSH Pro" with the IDs, prices and 3-day free trials above.
4. **Publish the two web pages** in `~/Projects/public_html/omnissh/` to your site, so the Privacy and Terms URLs work.
5. **Upload the builds**: Xcode → Window → Organizer → select each OmniSSH archive → Distribute App → App Store Connect → Upload.
6. **Fill in the listing** from this file, attach the screenshots, paste the review notes, and choose Submit for Review.
