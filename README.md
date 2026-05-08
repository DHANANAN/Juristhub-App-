# JuristHub Android App

> A native Android shell for [djlexfolio.tech](https://djlexfolio.tech) — surfaces the personal portfolio (essays, projects, research) on the go.
>
> This is a **rebrand of DjLexFolio** — same app, less personal name. Nothing else changed.

| | |
| --- | --- |
| **Package** | `com.juristhub.app` |
| **Version** | 1.10 (`versionCode` 2) |
| **minSdk** | 21 (Android 5.0) |
| **targetSdk** | 35 (Android 15) |
| **compileSdk** | 35 |
| **Loads** | `https://djlexfolio.tech` |

## Latest release

| File | Size | Path |
| --- | --- | --- |
| App Bundle (Play Store) | ~4 MB | [`releases/JuristHub-v1.10-release.aab`](releases/JuristHub-v1.10-release.aab) |
| Side-load APK | ~5 MB | [`releases/JuristHub-v1.10-release.apk`](releases/JuristHub-v1.10-release.apk) |
| Play Store icon (512×512) | — | [`play-store-icon-512.png`](play-store-icon-512.png) |

Both artifacts are signed with the upload key (see fingerprint below). Upload the **AAB** to Google Play; the APK is for direct install / QA.

## What it does

It's a thin WebView wrapper. That's the entire app:

1. Splash screen (~1.5s) showing the gold-J-on-navy mark.
2. Full-screen WebView pointed at `https://djlexfolio.tech`.
3. System UI hidden (immersive). Hardware back navigates the WebView's history.
4. External links (anything outside djlexfolio.tech / github.io) open in the user's default browser.
5. `mailto:` and `tel:` links hand off to system apps.

No accounts, no analytics SDK, no Firebase, no payments, no AI APIs — just the wrapped website. Whatever data the website itself collects (analytics, cookies) is governed by the website's own policy.

## Project structure

```
JuristHub-App/
├── app/
│   ├── build.gradle                         Module config (signing, deps, versions)
│   ├── proguard-rules.pro
│   └── src/main/
│       ├── AndroidManifest.xml              Permissions + activities
│       ├── java/com/juristhub/app/
│       │   ├── MainActivity.java            WebView shell
│       │   └── SplashActivity.java          1.5s splash → MainActivity
│       └── res/
│           ├── drawable/                    Adaptive icon vectors
│           ├── layout/                      activity_main, activity_splash
│           ├── mipmap-{m,h,x,xx,xxx}hdpi/   PNG launcher icons (legacy)
│           ├── mipmap-anydpi-v26/           Adaptive icon manifest (API 26+)
│           ├── values/                      strings, colors, themes
│           └── xml/                         backup_rules, data_extraction_rules
├── gradle/wrapper/                          Gradle 8.10.2 wrapper
├── build.gradle                             Project-level (AGP 8.7.3)
├── settings.gradle
├── gradle.properties
├── gradlew · gradlew.bat
├── releases/                                Pre-built AAB + APK
└── play-store-icon-512.png                  Play Console listing icon
```

## Brand

- **Background**: `#0C1020` (deep navy) → `#1A1A3F` (hero gradient)
- **Accent**: `#D4AF37` (gold) · `#E8C97A` (gold highlight)
- **Text**: `#EEF3F9` · `#C8DDF0` · `#8AA0BA`

Launcher icon: gold serif **J** on navy, with rounded-square + circular masks via the adaptive icon system.

## Build

See [BUILD.md](BUILD.md) for the full local-build flow. TL;DR:

```bash
# requires JDK 21 (Android Studio's bundled JBR works)
./gradlew bundleRelease assembleRelease
```

Outputs land in `app/build/outputs/bundle/release/app-release.aab` and `app/build/outputs/apk/release/app-release.apk`.

## Signing

The release build is signed via `keystore.properties` (gitignored). To build a signed release locally you need:

1. The release keystore — `juristhub-release.jks` at the project root (gitignored).
2. A `keystore.properties` next to it (also gitignored), formatted as:

   ```properties
   storeFile=juristhub-release.jks
   storePassword=...
   keyAlias=juristhub
   keyPassword=...
   ```

If `keystore.properties` is missing, the release build falls back to unsigned — useful for clean clones, but the resulting APK won't install.

**Upload key fingerprint (used since v1.00):**

```
SHA1   : 7E:F2:8A:74:FF:AC:14:CC:FC:BA:C3:35:C8:BC:90:1A:C5:F1:3E:4B
SHA256 : 05:73:88:0D:A2:31:3B:DA:1F:68:38:3D:90:70:38:F4:E8:77:45:7F:CB:3C:2C:7A:E3:E7:56:84:AB:5E:E5:FF
Validity: 2026-05-08 → 2053-09-23
```

## Permissions

| Permission | Reason |
| --- | --- |
| `INTERNET` | WebView loads the wrapped website |
| `ACCESS_NETWORK_STATE` | Detect connectivity for graceful fallback |

That's it — no camera, mic, location, contacts, or storage access.

## Privacy & legal

The app itself doesn't collect or transmit any personal data. The wrapped website may set cookies or run analytics under its own policy. See:

- [Privacy Policy](https://dhananan.github.io/JuristHub/privacy/)
- [Terms of Service](https://dhananan.github.io/JuristHub/terms/)
- [Account Deletion](https://dhananan.github.io/JuristHub/account-deletion/)
- [Support](https://dhananan.github.io/JuristHub/support/)

## Contact

- Email: <krishnashelkeintern@gmail.com>

---

&copy; 2026 JuristHub. All rights reserved.
