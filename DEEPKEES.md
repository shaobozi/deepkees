# deepkees Android build

Personal Android customization of FlClash v0.8.99, commit
`68c71b8ef9b7486a224972eb371ff153c6b2de0f`.

- Display name: `deepkees`.
- Android application ID: `app.deepkees.android`.
- Launcher icon: DeepSeek whale on white, adapted from the official logo SVG at
  https://github.com/deepseek-ai/DeepSeek-LLM/blob/main/images/logo.svg.
- About description omitted; upstream project and GPL-3.0 notices retained.

This remains a FlClash-based application. No AI functionality has been added.
Changing branding does not establish safety or guarantee any change to a HarmonyOS warning.

## GitHub build

Push to `main` or run **Build deepkees Android** from GitHub's Actions tab.
Download the `deepkees-android-arm64` artifact from a successful run and extract
`deepkees-arm64-v8a.apk`. This workflow builds only the Android ARM64 app.
The upstream build and website workflows are preserved under
`.github/upstream-workflows/` so they do not run in this repository.

The first build needs no signing secrets and uses development signing. Its
application ID is `app.deepkees.android.dev`. The temporary runner's signing key
is not retained, so later development builds may require uninstalling the older
copy first. Export profiles before uninstalling to preserve app data.

For stable updates, add the following repository Actions secrets before building:

- `KEYSTORE_BASE64`: your dedicated JKS signing key encoded as base64.
- `KEY_ALIAS`: its signing alias.
- `STORE_PASSWORD`: its store password.
- `KEY_PASSWORD`: its key password.

Secrets stay out of Git. Signed builds use `app.deepkees.android` and skip
Crashlytics symbol/mapping uploads because this fork has no production Firebase
project. No APK is published as a public release automatically.

## Local build prerequisites

The upstream release pins Flutter 3.47.4, Go 1.26.4 and Android NDK 28.2.13676358.
Also install JDK 17, Rust through rustup, Android SDK platform 37 and build tools.

The checked host has Java 9 on PATH and no Flutter, Go, Rust or Android SDK in the
checked locations. No APK, release key or signing configuration has been generated yet.

## Build

Initialize the pinned core using HTTPS without changing the tracked submodule URL:

```powershell
git -c 'url.https://github.com/.insteadOf=git@github.com:' submodule update --init --recursive
flutter pub get
flutter analyze --no-fatal-infos
flutter test test/widgets/ test/common/
dart setup.dart android --arch arm64
```

Tests require the native toolchains through the upstream build hooks.
The package build puts artifacts in `dist/`.

Before creating a signed release, generate a dedicated signing key in
`android/app/keystore.jks` and add `storePassword`, `keyAlias` and `keyPassword` to
the ignored `android/local.properties` file. Keep the key and credentials private
and backed up: future updates need the same key. Without release signing,
upstream falls back to debug signing and adds the `.dev` application ID suffix.

The bundled Google Services configuration contains upstream development placeholders,
with package IDs adjusted for this build; no production Firebase credentials were added.
Upstream update links still lead to FlClash releases, not deepkees updates.
The new package has separate app data; export/import profiles to migrate them.
