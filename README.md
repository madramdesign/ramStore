# ramStore

Patched [AltServer](https://github.com/altstoreio/AltStore) 1.8 (build 97) for Macs where the official app cannot sign in. The menu bar app is still named AltServer. ramStore is this build.

Official AltServer 1.8.1 fails on macOS 27 with `Apple.APIError -80045`, then HTTP 503. The sign-in request sends a Mac client identity, and Apple rejects it. This build takes Windows anisette data from `https://ani.jkcoxson.com` and sends that same identity to Apple, including `X-Xcode-Version` `11.2 (11B41)`.

Apple Silicon only. The binary is ad-hoc signed and not notarized. It is not an official AltStore release. Copyright © Riley Testut. License: [AGPL-3.0](LICENSE).

## Install

1. Download [ramStore-AltServer.zip](https://github.com/madramdesign/ramStore/releases/latest) from the latest release.
2. Unzip it. You should see `AltServer.app`.
3. Clear the quarantine flag, then open the app:

   ```sh
   xattr -dr com.apple.quarantine AltServer.app
   open AltServer.app
   ```

   If macOS still blocks it, right-click `AltServer.app`, choose Open, and confirm.
4. Move `AltServer.app` to `/Applications`.
5. Click the menu bar icon, sign in with the Apple ID you use for sideloading, and install AltStore on the iPhone.
6. Leave automatic updates off. The app still has the official Sparkle feed at `https://altstore.io/altserver/sparkle-macos.xml`. An update replaces this build with 1.8.1, which cannot sign in. If a Sparkle prompt appears, choose to skip it.

A two-factor code has to come from a trusted device. An SMS code can fail.

## Sideload with it

After AltStore is on the phone, use AltStore to install an IPA signed with an Apple Development certificate. A distribution certificate is rejected. On a fresh restore, trust the developer profile under Settings → General → VPN & Device Management if iOS shows that screen.

## What this build changes

Base source is [kisnner26/AltStore](https://github.com/kisnner26/AltStore) branch `fix/80045-windows-anisette` at [`ddb4415`](https://github.com/kisnner26/AltStore/commit/ddb4415cc6aeeb3145de33a29f7064292a45d895). AltSign is [rileytestut/AltSign](https://github.com/rileytestut/AltSign) at [`832d9ce`](https://github.com/rileytestut/AltSign/commit/832d9cec339ea6ef6891011833dbb7227b028a29).

On top of that:

- The Apple GSA lookup uses the Windows anisette client info. The base branch still sent a hardcoded Mac identity (`<Mac15,7> <macOS;27.0;26A5378j> …`). See [patches/gsa-lookup-client-info.diff](patches/gsa-lookup-client-info.diff).
- Developer-portal requests send `X-Xcode-Version: 11.2 (11B41)` instead of `27.0 (27A5218g)`. See [patches/xcode-version.diff](patches/xcode-version.diff).

The local Xcode build also pointed the linker at static OpenSSL and corecrypto libraries on this Mac. Those paths are not part of the sign-in fix.

| | |
| --- | --- |
| App | `AltServer.app` |
| Version | 1.8 (97) |
| Architecture | arm64 |
| Minimum system | macOS 14.0 |
| Anisette server | `https://ani.jkcoxson.com` |
| Zip SHA-256 | `186c0b630793ade10ab83262584ea93c8b4d8ba03e8f413e5540e7ae5dba4b17` |

The anisette server is a third party. Sign-in still goes to Apple. The anisette provisioning request goes to that server.
