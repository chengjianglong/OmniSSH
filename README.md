# OmniSSH

![OmniSSH: every machine you own, one tap from your pocket](docs/media/hero.png)

**Every machine you own, one tap from your pocket.**

OmniSSH is a multi-session terminal and SSH client for iPhone, iPad and Mac. It lets you control your desktops and servers from anywhere, with keys kept in the Secure Enclave and reachability over Tailscale.

## See it in action

<table>
  <tr>
    <th>iPhone</th>
    <th>iPad</th>
  </tr>
  <tr>
    <td width="34%"><img src="docs/media/demo-iphone.gif" alt="OmniSSH on iPhone: opening the demo machine, running neofetch, nvidia-smi, git log and top, then the terminal menu"></td>
    <td width="66%"><img src="docs/media/demo-ipad.gif" alt="OmniSSH on iPad: machine sidebar with live GPU status next to a terminal running commands"></td>
  </tr>
</table>

Both recordings use the built-in **demo machine**, a simulated terminal you can try without a server. Higher quality versions: [iPhone (MP4)](docs/media/demo-iphone.mp4), [iPad (MP4)](docs/media/demo-ipad.mp4).

## Highlights

![A real terminal in your pocket: keyboard bar, find, snippets, broadcast input and triggers](docs/media/feature-terminal.png)

![Every machine at a glance: live CPU, RAM, disk and GPU status](docs/media/feature-status.png)

![Make it yours: terminal themes, font size and cursor style](docs/media/feature-themes.png)

![iPhone, iPad and Mac](docs/media/feature-everywhere.png)

## Screenshots

| Home | Terminal | Demo machine |
|---|---|---|
| ![Home](docs/screenshots/app-store/iphone-1-home.png) | ![Terminal](docs/screenshots/app-store/iphone-2-terminal.png) | ![Demo machine](docs/screenshots/demo-terminal.png) |

| Live status | Web Ports | Split panes and find |
|---|---|---|
| ![Live status](docs/screenshots/live-status.png) | ![Web Ports](docs/screenshots/web-ports-detect.png) | ![Find and panes](docs/screenshots/find-and-panes.png) |

More in [docs/screenshots](docs/screenshots). Hostnames, IP addresses and user names in screenshots of real machines are blurred.

## Documentation

- [Research](docs/RESEARCH.md): survey of iOS SSH apps and Swift libraries, and the chosen stack (SwiftUI, Citadel/SwiftNIO SSH, SwiftTerm, Secure Enclave keys, Tailscale).
- [Mac setup](docs/MAC-SETUP.md): set up a Mac as an SSH target (Remote Login, Tailscale, tmux, keys only).
- [App Store listing](docs/APP-STORE-LISTING.md): listing text, review notes and screenshot sizes.
- [Demo server](docs/DEMO-SERVER.md): a locked-down server for App Review, if Apple asks for a real connection.

## Brand assets

- [assets/app-icon.jpg](assets/app-icon.jpg): app icon (1024 × 1024)
- [assets/omnissh-logo.jpg](assets/omnissh-logo.jpg): logo banner
- [docs/media](docs/media): README banner, feature images and demo recordings

The app's source code is not part of this repository yet.
