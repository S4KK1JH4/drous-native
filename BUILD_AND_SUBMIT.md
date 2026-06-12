# DROUS — build & submit guide

Your web app is now wrapped as a native app with **Capacitor**.

- App name: **DROUS**
- App ID (package / bundle id): **com.drous.app**
- Your HTML lives in **`www/index.html`** — that's the single source of truth.

When you change the app, edit `www/index.html`, then run:

```powershell
npx cap sync
```

That copies the latest HTML into the native projects. Then rebuild.

---

## ANDROID → Google Play (do this on your Windows PC)

### 1. Install Android Studio
Download from https://developer.android.com/studio and install with default options.
It bundles the JDK, Android SDK and Gradle (you don't have these yet).

### 2. Open the Android project
In Android Studio: **Open** → select the folder:
```
C:\Users\user\Desktop\drous-native\android
```
Let it finish "Gradle sync" (first time downloads components — a few minutes).

### 3. Set your version (every upload needs a higher versionCode)
Edit `android/app/build.gradle`:
```gradle
versionCode 1          // increment by 1 for every Play upload (2, 3, 4 …)
versionName "1.0.0"    // the human-facing version
```

### 4. Create a signing key (once) and build a signed bundle
Android Studio → **Build → Generate Signed App Bundle / APK → Android App Bundle**.
- Click **Create new…** keystore. Save the `.jks` file somewhere safe and **back it up** — if you lose it you can never update this app again.
- Remember the keystore password + key alias + key password.
- Choose **release**, finish.

Output (the file you upload):
```
android/app/release/app-release.aab
```

### 5. Upload to Play Console
1. https://play.google.com/console → **Create app** (name DROUS, app, free).
2. **Production → Create new release → upload `app-release.aab`.**
3. Fill the required sections:
   - **Privacy policy URL:** https://drous-backend-1.onrender.com/privacy.html
   - **Data safety** form: declare Camera/photos, account info (name/email via Google sign-in), and that note text is sent to a service provider (Google Gemini) for processing. Mark "encrypted in transit" and "users can request deletion."
   - Store listing: short/full description, screenshots, 512×512 icon, feature graphic.
4. Send for review.

---

## iOS → App Store (requires a Mac)

You **cannot** build iOS on Windows. On a Mac with Xcode + CocoaPods:

```bash
# in the drous-native folder, on the Mac:
npm install
npx cap add ios
npx cap sync ios
npx cap open ios
```

Then in Xcode: set the team/signing (your Apple Developer account), set the version,
**Product → Archive**, and upload to App Store Connect via the Organizer.

- **Privacy policy URL:** https://drous-backend-1.onrender.com/privacy.html
- Fill the **App Privacy** ("nutrition label"): Camera, Photos, Contact Info (name/email), User Content.
- Note: in `ios/App/App/Info.plist` add usage strings (Xcode will warn if missing):
  - `NSCameraUsageDescription` = "DROUS uses the camera to scan your notes into flashcards."
  - `NSMicrophoneUsageDescription` = "DROUS uses the microphone for in-app calls with friends."

---

## Before you ship — known notes
- The app loads libraries (Tailwind, Tesseract OCR, fonts, Firebase) from the internet at
  runtime, so first use needs a connection.
- The **live camera** (getUserMedia) inside a WebView sometimes needs extra permission
  handling; the "pick a photo" path works out of the box. Test the snap flow on a real device.
- App icons are still the Capacitor default — replace them before publishing
  (Android Studio: right-click `res` → New → Image Asset; iOS: Assets.xcassets).
- Apple Guideline 4.2: make sure the app feels like an app (it does — camera OCR, offline
  study), or review may push back on "just a website."
