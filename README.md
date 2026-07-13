# 🎈 Bubble Pop ABC — Android App

This is a native Android project that packages the web game (HTML/CSS/JS)
into a real installable Android app, using a full-screen WebView. The game
runs 100% locally on the device — no internet connection needed to play,
and no data ever leaves the phone.

## Getting an actual .apk file

There are two ways to turn this project into an installable `.apk`. Pick
whichever suits you — neither requires you to write any code.

### Option A — No install needed: let GitHub build it for you (~5 minutes)

This project includes a ready-made GitHub Actions workflow
(`.github/workflows/build-apk.yml`) that compiles the APK in the cloud.

1. Create a free account at [github.com](https://github.com) if you don't
   have one.
2. Create a new **empty** repository (any name, e.g. `bubble-pop-abc`).
3. Upload this entire project folder into it — easiest way: on the
   repo's page click **"Add file" → "Upload files"**, then drag in
   everything from the unzipped `bubble-pop-abc-android` folder (including
   the hidden `.github` folder — if your browser/OS hides it, use
   `git` instead, see below, or a desktop tool like GitHub Desktop which
   shows hidden folders).
   - *Alternative if you're comfortable with git:*
     ```bash
     cd bubble-pop-abc-android
     git init
     git add .
     git commit -m "Bubble Pop ABC"
     git branch -M main
     git remote add origin https://github.com/YOUR-USERNAME/bubble-pop-abc.git
     git push -u origin main
     ```
4. In your repo on GitHub, click the **"Actions"** tab. A workflow run
   called "Build APK" should start automatically (or click **"Run
   workflow"** if it doesn't).
5. Wait a few minutes for it to finish (green checkmark ✅).
6. Click into the finished run → scroll down to **"Artifacts"** →
   download **`bubble-pop-abc-debug-apk`**. Unzip it — inside is
   `app-debug.apk`.
7. Copy that `.apk` to your Android phone (email it to yourself, use a
   USB cable, Google Drive, etc.) and tap it to install. You'll need to
   allow **"Install unknown apps"** for whichever app you used to open it
   (Android will prompt you the first time — it's a normal step for apps
   installed outside the Play Store).

This produces a **debug-signed APK** — perfect for installing on your own
phone(s) to play, but not meant for publishing to the Play Store (that
needs a proper release signing key — see Option B).

### Option B — Build it yourself in Android Studio (also gives you a
release build / Play Store option)

You'll need **Android Studio** (free, from developer.android.com) — it
downloads the correct Gradle/SDK versions automatically the first time you
open the project, so you don't need anything pre-installed beyond Android
Studio itself.

1. Unzip this project.
2. Open Android Studio → **File → Open** → select the unzipped
   `bubble-pop-abc-android` folder.
3. Wait for Gradle Sync to finish (first time may take a few minutes while
   it downloads the Gradle distribution and Android SDK components — it
   needs an internet connection for this one-time setup step).
4. For a quick installable APK: **Build → Build Bundle(s)/APK(s) → Build
   APK(s)**. When it finishes, click the **"locate"** link in the
   notification to find `app-debug.apk`.
5. Or, to run it directly: plug in an Android phone (with USB debugging
   enabled) or start an emulator, then click the green ▶ **Run** button.

### Building a signed release APK/AAB to publish

In Android Studio: **Build → Generate Signed Bundle / APK…**, follow the
wizard to create (or reuse) a signing key, and choose APK for direct
install-on-device sharing, or Android App Bundle (AAB) if you plan to
publish to the Google Play Store.

## What's different from the plain web version
- Runs as a real app with its own launcher icon, no browser UI/address bar.
- Text-to-speech (letters/words spoken aloud) uses Android's **native**
  TextToSpeech engine via a small JS↔Java bridge (`window.AndroidTTS`),
  which is far more reliable across devices than in-browser speech.
- All progress is saved on-device with `localStorage` inside the WebView
  (per-app storage, survives app restarts; cleared only if you clear the
  app's storage/data in Android Settings, or uninstall it).
- CSS was tuned with extra breakpoints for small phone screens and
  landscape mode, plus touch tweaks (no double-tap zoom, no accidental
  text selection, safe-area padding for notches).
- The optional PHP save/sync endpoints from the web version aren't
  included here (there's no server on a phone) — everything works from
  `localStorage` alone.

## Project structure

```
bubble-pop-abc-android/
├── .github/workflows/build-apk.yml  Cloud APK build (GitHub Actions)
├── app/
│   ├── build.gradle                 App module config (SDK versions, deps)
│   └── src/main/
│       ├── AndroidManifest.xml
│       ├── java/com/kidsgame/bubblepopabc/
│       │   └── MainActivity.java    WebView host + native TTS bridge
│       ├── res/
│       │   ├── layout/activity_main.xml   Full-screen WebView layout
│       │   ├── mipmap-*/ic_launcher.png   Generated app icon (all densities)
│       │   └── values/                    Strings, colors, theme
│       └── assets/www/              The full game (same as the web version):
│           ├── index.html, levels.html
│           ├── css/style.css
│           ├── js/ (game.js, audio.js, levels-config.js, items-data.js)
│           ├── data/items.json
│           └── images/A.svg … Z.svg
├── build.gradle / settings.gradle / gradle.properties
└── gradle/wrapper/gradle-wrapper.properties
```

## Customizing

- **App name / package id**: change `applicationId` in `app/build.gradle`
  and `app_name` in `res/values/strings.xml`. If you rename the package,
  also move the `MainActivity.java` file to match and update its
  `package` declaration.
- **Icon**: replace the PNGs in each `res/mipmap-*` folder (keep the same
  filenames/sizes: 48/72/96/144/192px).
- **Gameplay/levels/art**: edit the files under `app/src/main/assets/www/`
  directly — they're the exact same HTML/CSS/JS as the standalone web
  version, so anything you learned tweaking that applies here too.
- **Orientation**: the app currently allows both portrait and landscape
  (`android:screenOrientation="unspecified"` in the manifest) since the
  CSS adapts to both. Force one orientation there if you'd prefer.

## Troubleshooting

- **Gradle sync fails / can't download distribution**: this first-time
  step needs internet access; Android Studio also lets you pick a
  different installed Gradle version via *File → Project Structure* if
  your network blocks the default download URL.
- **No sound/voice on emulator**: emulators sometimes ship without a
  TTS voice installed — on a real device it works out of the box; on an
  emulator, open its Settings → Accessibility/Language & Input →
  Text-to-speech and install the Google/Android TTS voice data.
- **Blank white screen**: make sure `app/src/main/assets/www/index.html`
  exists — Android Studio's "Open" dialog must point at the project's
  *root* folder (the one containing `settings.gradle`), not the `app`
  subfolder.
