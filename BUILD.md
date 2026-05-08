# Build instructions

How to build the JuristHub Android app from source.

## Prerequisites

- **JDK 21** (LTS). Android Studio bundles one at `<AS install>/jbr/`. JDK 17 also works.
  - JDK 25 does **not** work with the bundled Gradle 8.10.2 — error: *"Unsupported class file major version 69"*.
- **Android SDK** with:
  - Platform `android-34` (or higher)
  - Build-tools `34.0.0` (or higher)
  - Platform-tools (`adb`)
- **Git** (only needed if you cloned the repo).

The Gradle wrapper handles Gradle itself — no separate Gradle install needed.

## One-time setup

1. Clone this repo.

2. Create `local.properties` at the project root pointing at your Android SDK:

   ```properties
   sdk.dir=C\:\\Users\\<you>\\AppData\\Local\\Android\\Sdk
   ```

   (Backslashes must be escaped on Windows.)

3. To build a **signed** release, also create `keystore.properties` at the project root:

   ```properties
   storeFile=juristhub-release.jks
   storePassword=<your-store-password>
   keyAlias=juristhub
   keyPassword=<your-key-password>
   ```

   And place `juristhub-release.jks` at the project root. Both files are gitignored.

   To **generate a new keystore**:

   ```bash
   keytool -genkeypair -v \
     -keystore juristhub-release.jks \
     -alias juristhub \
     -keyalg RSA -keysize 4096 -validity 10000 \
     -storetype PKCS12 \
     -dname "CN=JuristHub, OU=App, O=JuristHub, L=Indore, ST=Madhya Pradesh, C=IN"
   ```

## Build commands

Set `JAVA_HOME` to your JDK 21 (PowerShell example):

```powershell
$env:JAVA_HOME = "C:\Program Files\Android\Android Studio\jbr"
$env:PATH = "$env:JAVA_HOME\bin;$env:PATH"
```

### Debug APK (no signing config needed)

```bash
./gradlew assembleDebug
# → app/build/outputs/apk/debug/app-debug.apk
```

### Signed release APK (for direct install)

```bash
./gradlew assembleRelease
# → app/build/outputs/apk/release/app-release.apk
```

### Signed AAB (for Play Store upload)

```bash
./gradlew bundleRelease
# → app/build/outputs/bundle/release/app-release.aab
```

### Both, in one command

```bash
./gradlew bundleRelease assembleRelease
```

First build downloads ~200 MB of Gradle + Android dependencies and takes 5–10 minutes. Subsequent builds finish in 30–60 seconds.

## Verify a signed build

```bash
keytool -printcert -jarfile app/build/outputs/apk/release/app-release.apk
```

Should print the upload-key fingerprint (see [README.md](README.md#signing)).

## Submitting to Google Play

1. Create the app in the [Play Console](https://play.google.com/console).
2. **App content** → fill out Privacy Policy URL, Data Safety, content rating, target audience.
   - Privacy URL: <https://dhananan.github.io/JuristHub/privacy/>
   - Account deletion URL: <https://dhananan.github.io/JuristHub/account-deletion/>
3. **Production** → **Create new release** → upload `releases/JuristHub-v1.00-release.aab`.
4. Fill in **What's new** notes, the 512×512 icon ([play-store-icon-512.png](play-store-icon-512.png)), feature graphic, screenshots.
5. **Submit for review.** First review typically takes 1–7 days.

## Troubleshooting

**"Unsupported class file major version 69"**
Your JDK is too new for Gradle 8.10. Use JDK 21 (Android Studio's JBR).

**"SDK location not found"**
You're missing `local.properties`. See setup step 2.

**"Keystore was tampered with, or password was incorrect"**
`keystore.properties` has wrong passwords, or `storeFile` points to the wrong path.

**"Manifest merger failure: package conflict"**
Stale `build/` cache. Run `./gradlew clean` and rebuild.
