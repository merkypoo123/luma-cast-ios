# Luma Cast — standalone Google Cast iPhone app

A new native iPhone app written from scratch, using Kayle Cast only as a feature and interaction reference. This version uses Google Cast and the TV's built-in Chromecast receiver. It does not use AirPlay, require the PC during playback, replace the TV home screen, or change its mute setting.

## Version 0.2.2: visible Cast status

Removed Open source page from Videos as requested; title and source labels remain. Player now shows the live status directly below the title and no longer equates connection with playback. A local, bounded Cast activity log records load submission, acknowledgement, failure, timeout and receiver state, and the app version/build is visible. The media client and active load request are retained, and each selection refreshes the live SDK session. This version needs a physical iPhone retry to diagnose the reported phone-only sample failure; no successful phone playback is claimed.

## Version 0.2.1: source labels and playback status

Videos now use the main browser page title and show the source website, embedded player host, media server and filename separately. If a site does not supply a movie title, the app cannot infer one from an opaque stream filename.

Cast loads include both content ID and content URL, and status matching accepts either field. Browser playback pauses only after the TV reports playing. Failed requests show a numeric error, loading has a 45-second timeout, and selected media can be retried. The public sample is now MDN's short flower clip; the previous W3C sample failed on the target TCL receiver while this clip played through the same receiver in a desktop protocol test. Physical iPhone verification of this version remains required; Doraby playback is not yet confirmed.

## Version 0.2: Google Cast

- Native Google Cast button for discovering, connecting and disconnecting TVs.
- Google's Default Media Receiver (CC1AD845); no custom receiver registration or extra TV app is required.
- Cast sessions, reconnection, receiver playback status and errors handled through Google's iOS SDK.
- Selected videos wait for the user to choose a TV, then load directly on it.
- TV play/pause/stop, timeline, back 10 / forward 30 seconds and exact-time seeking. Seek availability comes from the receiver's reported supported commands.
- Google's expanded remote controls, including TV volume. The app does not automatically alter or unmute TV audio.
- In-app browser, saved pages, complete-stream detection, basic ad filters and popup blocking.
- HLS, DASH, MP4, WebM, MOV and audio URL detection. Actual container and codec support depends on the TV. Initialization fragments and media segments are excluded.

AirPlay player UI, local AVPlayer playback and background-audio mode have been removed. WebKit AirPlay playback is explicitly disabled. The AirPlay app previously installed on the TV is separate and is not uninstalled by this change.

## Connect and test

1. Sign and install the iPhone build. The minimum OS is iOS 17.
2. Connect the phone to the same Wi-Fi as the TV.
3. Tap the Cast icon and allow iOS Local Network access when requested.
4. Choose Kayle (the TCL's Chromecast name may appear with different capitalization).
5. In Videos, choose **Try a public sample video**. Confirm video on the TV and advancing time in Player, then test pause and forward/backward seeking.
6. After the public sample works, browse the intended website, start its player to discover a complete stream, open Videos and choose **Cast to TV**. Browser playback does not need to finish loading first.

## Source and build

The complete source tree and app icons are in `Source.zip` in the initial browser-based GitHub upload. It contains no Windows runtime, browser profile, cookies, credentials, or personal browsing history. The `.github/workflows/ios.yml` workflow extracts it before building.

The project generator in `scripts/make_project.py` creates the included Xcode project and adds the official GoogleCastDynamic Swift package pinned to SDK 4.8.6. Swift Package Manager downloads and verifies Google's binary SDK and resolves its GTMSessionFetcher dependency. Google SDK terms and third-party licenses apply. No SDK binary is committed to this repository. The package URL is https://github.com/googlecast/google-cast-ios-sdk.

The workflow runs on a standard GitHub-hosted macOS runner, tests JS detection and native URL/MIME/time handling, validates the Xcode project, archives for physical iPhone and packages an unsigned IPA. The artifact expires after three days. Standard runner execution is free for this public repository; no paid runner is configured.

The IPA needs local signing and provisioning using a compatible sideloading tool before installation. Apple credentials or signing keys should never be committed. Free-account sideloading normally requires renewal every seven days. Building on GitHub does not remove Apple's provisioning requirements.

## Playback limits

The TV downloads the selected media URL itself. Browser-only cookies, required referrers, geo/IP restrictions, expired links, CORS restrictions, unsupported codecs, DRM and anti-bot checks can prevent playback. Detection is not a playback guarantee. This version does not relay or transcode video through the phone, and does not include the PC app's FFmpeg compatibility conversion. A browser working on iPhone does not guarantee the receiver can fetch its video URL.

Live streams may not report a duration or allow seeking; the timeline stays disabled unless the receiver reports support. Ad filtering uses a small bundled domain list, not full EasyList; it cannot remove ads embedded in video. There is no local-file import, subtitle-file import, queue or custom Cast receiver in this milestone.

## Checks and privacy

Run `node --test Tests/detection.test.cjs` on Windows or macOS. On a Mac, run `swiftc LumaCast/MediaRules.swift Tests/RulesChecks.swift -o /tmp/luma-rules` followed by `/tmp/luma-rules`.

Bookmarks and WebKit data stay in the iPhone sandbox. This app has no custom cloud backend or account login. Normal website requests and Google Cast SDK traffic still occur. Delete the iPhone app to remove its local data. TV software is managed separately.
