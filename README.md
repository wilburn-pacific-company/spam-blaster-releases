# SpamBlaster

Local spam filtering for Hotmail, Exchange, Gmail, iCloud, Yahoo, Fastmail, and any IMAP
provider. SpamBlaster consists of an extremely lightweight spam filtering hub written in Rust.
It includes a robust dashboard and add-ins for Outlook, Apple Mail and Thunderbird. It is
completely local with all filtering performed on your machine. Mail is downloaded to your machine
directly from your provider. No cloud, no tracking, no data collection. Your rules, your filter.

## Download

**[spamblaster.org/download](https://spamblaster.org/download)** is the place to start. One
click for your platform, with the current version and its SHA-256 shown on the page.

Every build is also attached to a
[release](https://github.com/wilburn-pacific-company/spam-blaster-releases/releases) here, along
with its checksums, software bill of materials and build attestation.

## What each download contains

| Download | Contains |
|---|---|
| **Windows installer** (`.exe`) | The daemon, the dashboard, the classic-Outlook add-in and the Outlook web panes. Start here on Windows. |
| **macOS app** (`.dmg`) | The daemon, the dashboard, the web panes, and Blast for Apple Mail, all in one application. Start here on macOS. |
| **Thunderbird add-on** (`.xpi`) | The Thunderbird add-in. It talks to the daemon, so it needs one of the installers above. |

The daemon is the hub. The add-ins are thin: they hand a blocked sender to the daemon, and the
daemon does the filtering. You only need the add-in for the mail app you actually use.

## Requirements

- Windows 10 or 11 (x64), or macOS 14 or later on Apple silicon.
- A mailbox reachable by Microsoft Graph (Hotmail, Outlook.com, Exchange, Microsoft 365) or by
  IMAP with an app password (Gmail, iCloud, Yahoo, Fastmail and most other providers).
- Thunderbird 115 or later, if you want the Thunderbird add-on.

## Verifying a download

Every release publishes SHA-256 checksums, CycloneDX software bills of materials, and a signed
SLSA build-provenance attestation. The Windows binaries are Authenticode signed, and the macOS
binaries are Apple Developer ID signed and notarized.

See **[VERIFYING.md](VERIFYING.md)** for the commands.

## About this repository

This repository distributes SpamBlaster's signed binaries. **It does not contain the source
code.** SpamBlaster is a closed-source product of Wilburn Pacific Company, built from a private
repository. Each release records the exact commit it was built from, so that a signature or an
attestation can be tied to a specific build.

Issues are not tracked here. Please use the links below.

## Support

- **Bugs and feature requests:** [spam-blaster-issues](https://github.com/wilburn-pacific-company/spam-blaster-issues)
- **Questions and setup help:** [spamblaster.org/support](https://spamblaster.org/support)
- **Contact:** [spamblaster.org/contact](https://spamblaster.org/contact)

## Licence

SpamBlaster is free for personal use. Commercial and enterprise use requires a paid licence.
See the [end user licence agreement](https://spamblaster.org/eula) and the
[privacy policy](https://spamblaster.org/privacy).

Copyright Wilburn Pacific Company. All rights reserved.
