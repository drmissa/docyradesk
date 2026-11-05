# DocyraDesk Rebrand Report

This document records all changes applied during the rebranding of RustDesk 1.5.1 to DocyraDesk, following the strict behavioral-safety requirements in `BRIEF-REBRAND.md`.

---

## 1. Task 1 Verification — Self-Hosted Relay (hbbs / hbbr)

### Public IP
`38.242.249.73`

### Command Lines Used
Spawned in the background under working directory `/opt/data/apps/docyra-relay/data/`:
```bash
nohup /opt/data/apps/docyra-relay/bin/hbbs -r 38.242.249.73:21117 -p 21116 > /opt/data/apps/docyra-relay/data/hbbs.log 2>&1 &
nohup /opt/data/apps/docyra-relay/bin/hbbr -p 21117 > /opt/data/apps/docyra-relay/data/hbbr.log 2>&1 &
```

### Public Key (`id_ed25519.pub`)
```
6ojDwGMBaXQz02+ymvQDQjV3cCTBEcn5lS4LfSBgHRQ=
```

### Active Listeners
Inspection of network listeners confirms all relay/rendezvous ports are active:
- `TCP6 :21115` (NAT test port) - `LISTEN`
- `TCP6 :21116` (hbbs ID rendezvous server) - `LISTEN`
- `UDP6 :21116` (hbbs ID rendezvous server) - `ACTIVE`
- `TCP6 :21117` (hbbr relay server) - `LISTEN`
- `TCP6 :21118` (hbbs WebSocket) - `LISTEN`
- `TCP6 :21119` (hbbr WebSocket) - `LISTEN`

---

## 2. Task 2 — Rebrand Allowlist & Denylist

### Allowlist (Files Touched and Why)

1. **`Cargo.toml`**
   - Updated package description to `DocyraDesk Remote Desktop`.
   - Updated `[package.metadata.winres]` properties:
     - `ProductName = "DocyraDesk"`
     - `FileDescription = "DocyraDesk Remote Desktop"`
     - `OriginalFilename = "DocyraDesk.exe"`
     - `LegalCopyright = "Copyright © 2026 Docyra. All rights reserved."`
   - Updated `[package.metadata.bundle]` properties:
     - `name = "DocyraDesk"`
     - `identifier = "com.docyra.desk"`
   - *Why*: User-facing app and bundle metadata, Windows binary properties, and installer naming.

2. **`flutter/windows/runner/Runner.rc`**
   - Updated `CompanyName` to `"Docyra"`.
   - Updated `FileDescription` to `"DocyraDesk Remote Desktop"`.
   - Updated `InternalName` to `"DocyraDesk"`.
   - Updated `OriginalFilename` to `"DocyraDesk.exe"`.
   - Updated `ProductName` to `"DocyraDesk"`.
   - Updated `LegalCopyright` to `"Copyright © 2026 Docyra. All rights reserved."`.
   - *Why*: Windows executable version resource displayed in Explorer file properties and Task Manager.

3. **`flutter/windows/runner/main.cpp`**
   - Updated fallback `app_name` string from `L"RustDesk"` to `L"DocyraDesk"`.
   - *Why*: Window title and singleton mutex fallback if core DLL fails to supply name.

4. **`flutter/linux/my_application.cc`**
   - Updated header bar title and window title from `"rustdesk"` to `"DocyraDesk"`.
   - *Why*: Linux GTK window header and title displayed to the user.

5. **`flutter/linux/CMakeLists.txt`**
   - Updated `APPLICATION_ID` from `"com.carriez.flutter_hbb"` to `"com.docyra.desk"`.
   - *Why*: Sets GTK application ID per target identity.

6. **`flutter/macos/Runner/Configs/AppInfo.xcconfig`**
   - Updated `PRODUCT_NAME` to `DocyraDesk`.
   - Updated `PRODUCT_BUNDLE_IDENTIFIER` to `com.docyra.desk`.
   - Updated `PRODUCT_COPYRIGHT` to `Copyright © 2026 Docyra. All rights reserved.`.
   - *Why*: macOS build configuration metadata and window title.

7. **`flutter/macos/Runner.xcodeproj/project.pbxproj`**
   - Updated `PRODUCT_BUNDLE_IDENTIFIER` across targets to `com.docyra.desk`.
   - *Why*: macOS application bundle identity.

8. **`flutter/ios/Runner.xcodeproj/project.pbxproj`**
   - Updated `PRODUCT_BUNDLE_IDENTIFIER` across targets to `com.docyra.desk`.
   - *Why*: iOS application bundle identity.

9. **`flutter/ios/Runner/Info.plist`**
   - Updated `CFBundleDisplayName` and `CFBundleName` to `DocyraDesk`.
   - *Why*: Display name on iOS home screen and app switchers. Kept `rustdesk` URL scheme intact.

10. **`flutter/android/app/build.gradle`**
    - Updated `applicationId` to `"com.docyra.desk"`.
    - *Why*: Android application package ID per target identity.

11. **`flutter/android/app/src/main/AndroidManifest.xml`**
    - Updated `android:label` for Application to `"DocyraDesk"`.
    - Updated `android:label` for InputService to `"DocyraDesk Input"`.
    - *Why*: Android app launcher label and accessibility settings display. Kept deep link scheme `rustdesk` intact.

12. **`flutter/android/app/src/main/res/values/strings.xml`**
    - Updated `app_name` to `"DocyraDesk"`.
    - Updated accessibility service and foreground service user-facing descriptions.
    - *Why*: Android user-visible localized string values.

13. **`res/rustdesk.desktop` & `res/rustdesk-link.desktop`**
    - Updated `Name` field from `RustDesk` to `DocyraDesk`.
    - *Why*: Linux desktop application launcher menu entry and protocol handler entry name. Kept binary and icon references intact for system paths.

14. **`flutter/pubspec.yaml`**
    - Updated `description` to `DocyraDesk Remote Desktop`.
    - *Why*: Package documentation/metadata without churning internal package import name.

15. **`flutter/lib/desktop/pages/desktop_setting_page.dart`**
    - Updated website URL button to `'https://docyradesk.com'`.
    - Updated copyright text to `"Docyra"`.
    - *Why*: About box in Desktop Settings.

16. **`flutter/lib/mobile/pages/settings_page.dart`**
    - Updated website URL in About dialog to `'https://docyradesk.com'`.
    - Updated display link text to `'docyradesk.com'`.
    - *Why*: About box in Mobile Settings.

17. **`flutter/lib/common.dart`**
    - Updated `launchUrl(Uri.parse('https://docyradesk.com'))` for website link.
    - *Why*: User-facing website link navigation.

18. **`src/common.rs`**
    - Configured default settings in `global_init()`:
      - `custom-rendezvous-server`: `"38.242.249.73"`
      - `relay-server`: `"38.242.249.73:21117"`
    - *Why*: Directs fresh installations to our self-hosted relay out-of-the-box as requested in Task 2 Rule 4.

---

### Denylist (Files & Identifiers Deliberately Left Alone and Why)

1. **`rustdesk://` URL Scheme and Parsers**
   - Kept in `flutter/android/app/src/main/AndroidManifest.xml`, `flutter/ios/Runner/Info.plist`, `flutter/macos/Runner/Info.plist`, `flutter/lib/common.dart`, `res/rustdesk-link.desktop`.
   - *Why*: Strict rule 1 — deep linking and browser integration schemes must remain 100% compatible.

2. **Configuration Keys & Storage Identifiers**
   - Config option keys (`custom-rendezvous-server`, `relay-server`, `api-server`, `key`, `stop-service`, `direct-server`, etc.) in `libs/base/src/config/keys.rs` and throughout the core.
   - *Why*: Strict rule 1 — changing config keys breaks backwards compatibility and existing configuration persistence.

3. **Registry Paths & Service Names**
   - `HKLM\Software\RustDesk\...`, `Software\Microsoft\Windows\CurrentVersion\Uninstall\...`
   - *Why*: Strict rule 1 — preserves existing installed state, uninstallation hooks, and avoids orphaning configuration.

4. **Update Endpoints & Metadata Schemas**
   - `src/updater.rs`, `src/update_metadata.rs`, `src/manual_update.rs`, `https://api.rustdesk.com/version/latest`, `PUBLIC_MACOS_BUNDLE_ID = "com.carriez.rustdesk"`.
   - *Why*: Strict rule 1 — update check mechanisms and signature validations rely on exact schema keys and package IDs.

5. **Rendezvous / Relay Protocol Strings & Binaries**
   - `hbbs`, `hbbr`, `rendezvous_proto`, protobuf definitions in `libs/base/protos/message.proto`.
   - *Why*: Strict rule 1 — wire protocol interoperability between clients and servers.

6. **Submodule `libs/hbb_common`**
   - Left 100% untouched (`git diff libs/hbb_common` is empty).
   - *Why*: Project guideline — `libs/hbb_common` is a git submodule shared with the server repository. Modifying it causes upstream drift and costs round trips.

7. **Dart Package Internal Imports (`package:flutter_hbb/...`)**
   - Dart package name left as `flutter_hbb` in `flutter/pubspec.yaml`.
   - *Why*: Changing the Dart package name breaks 118+ internal Dart file imports and codegen bindings without any end-user visibility benefit.

8. **ExternalParked Project `/opt/data/projects/docyradesk`**
   - Completely untouched.
   - *Why*: Explicit instruction in brief.

9. **Git Remotes**
   - No git push executed.
   - *Why*: Explicit instruction in brief.
