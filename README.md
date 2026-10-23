# Luma Cast — iPhone prototype

A new native iPhone app, written from scratch in SwiftUI/WebKit/AVFoundation. Kayle Cast was used only as a feature and interaction reference. No Windows application files, Electron runtime, browser profile, browsing history, or credentials are bundled.

## Current milestone

The source and GitHub Actions workflow are prepared. The initial GitHub upload includes the complete readable source tree in `Source.zip`; the workflow extracts it before building. The archive contains source and app icons, not a compiled app or user data. Windows checks cover injected media detection. The Xcode build and playback on a real iPhone have not yet been verified. This is not a finished or installable release until the macOS workflow succeeds and its output is signed.

## Features in this prototype

- Browser with normal back/forward/reload controls and saved pages.
- Detection of complete HLS, MP4, MOV, M4A and MP3 URLs from media elements and observable web requests, including subframes.
- Fragment filtering, preservation of signed query strings, a bounded URL list, and a URL-only web-to-native bridge.
- Basic bundled ad-domain filters and popup blocking, with an on/off switch. These filters are smaller than EasyList and do not remove ads embedded in a video stream.
- Direct-link playback and a built-in public Sintel sample for the first TV test.
- Native media player with AirPlay route picker, play/pause/stop, seek bar, back 10 / forward 30 seconds, and exact time entry.
- Background audio support. No PC server is involved during playback.

## How it connects to Kayle

The first version uses media AirPlay, not screen mirroring. The existing AirPlay receiver on the TV must be running, and the phone must be on the same Wi-Fi. Open a detected video, then tap the AirPlay icon and select Kayle. The interface reports whether AVPlayer is actually in external playback mode rather than assuming that opening a video connected a TV.

This does not implement Google Cast, DLNA, a phone-hosted media proxy, DASH playback, local file import, queueing, or FFmpeg conversion. It does not modify TV settings or install TV apps. Those are separate features, not hidden dependencies.

Browser detection does not mean a URL will play in AVPlayer. Browser-only cookies, referrer restrictions, unsupported codecs, expired links, DRM and anti-bot challenges can prevent playback. WebKit does not provide the same network interception as Electron. Where available, try the website's own AirPlay player control instead. Keeping the phone available may still be necessary for a particular AirPlay session; this is not a guarantee that every source plays after force-closing the app.

## Build using GitHub, with no personally owned Mac

The included project targets iOS 17 or later and uses only Apple frameworks. There are no app dependencies or external SDK downloads.

1. Review the source, then create a GitHub repository and upload this folder. Public source is visible to everyone; standard GitHub-hosted macOS runner execution is free for public repositories. Private repositories use the account's allowance. Publishing needs the owner's explicit approval.
2. The workflow `.github/workflows/ios.yml` runs manually or on a push to `main`.
3. The job tests web detection and native URL/time parsing, validates the project, then performs an unsigned iPhone archive using Xcode.
4. Download the `LumaCast-unsigned-iPhone` artifact. Inside is `LumaCast-unsigned.ipa`.
5. This IPA is NOT directly installable. A compatible sideloading tool must sign and provision it using your Apple account before installing it. Do Apple-account sign-in and device trust prompts yourself; do not upload Apple credentials or private signing keys to this repository.
6. Free-account sideloading normally requires renewal every seven days. Building an IPA on GitHub does not remove Apple's provisioning requirements.

No App Store submission, paid developer membership, paid runner, hosted service subscription, or account creation is performed by this project.

## First real-device test

1. Install the signed test build on the iPhone. Enable Developer Mode if iOS requests it.
2. Ensure the TV's AirPlay receiver is running and both devices are on the same LAN.
3. In Videos, choose **Try a public sample video**.
4. In Player, tap AirPlay and choose **Kayle**. Confirm the picture appears on the TV and the app reports AirPlay active.
5. Try pause/resume and seeking forward and backward. Keep the existing TV mute state.
6. Test the intended website only after this basic sample works. Do not assume success on the sample proves that browser-protected movie links work.

## Local checks

On any machine with Node.js:

    node --test Tests/detection.test.cjs

Generate the Xcode project with Python 3 (the generated project is already included):

    python3 scripts/make_project.py

On a Mac, run the native parsing checks:

    swiftc LumaCast/MediaRules.swift Tests/RulesChecks.swift -o /tmp/luma-rules
    /tmp/luma-rules

Or open `LumaCast.xcodeproj` in Xcode, choose your signing team, and build to a device.

## Privacy and removal

Bookmarks, website cookies and website storage stay in the iPhone app's sandbox. The app has no analytics, advertising SDK, custom cloud backend or Apple-account login. Websites you open still receive normal web requests. Deleting the iPhone app removes its local data. TV receiver installation and removal are separate.
