# MIND FORGE — AI Assisted Learning Platform

Welcome to the **MIND FORGE** repository! MIND FORGE is an innovative, AI-assisted learning platform built to connect students, teachers, parents, and administrators in a single, cohesive educational ecosystem.

## 1. Project Overview

**What the project does:**
MIND FORGE is a comprehensive mobile application designed specifically for ICSE students (Grades 8–10). It provides an interactive platform for learning various subjects like Mathematics, Physics, Chemistry, and more.

**Key Features and Purpose:**
* **Role-Based Dashboards:** Dedicated interfaces for Students, Teachers, Parents, and Admins.
* **Real-time Communication:** Uses WebSockets for real-time updates and interactions.
* **Progress Tracking:** Interactive charts and visualizations for academic progress.
* **Document Management:** Support for PDF generation, PDF sharing, and image uploads.

**Problem it solves:**
It bridges the communication and tracking gap between students, educators, and parents, providing personalized learning insights and a centralized tracking system.

## 2. Tech Stack

* **Framework:** Flutter (SDK >=3.3.0)
* **Language:** Dart
* **State Management:** Riverpod (with Annotations & Generator)
* **Routing:** GoRouter
* **Networking/Backend:** Dio for HTTP requests, `web_socket_channel` for WebSockets
* **Local Storage:** SharedPreferences, Flutter Secure Storage
* **UI & Theming:** Custom Material/Cupertino Widgets, Google Fonts, FL Chart for analytics

## 3. Project Structure

The project follows a feature-first architectural approach:

```text
frontend/
├── android/        # Android-specific build configurations
├── ios/            # iOS-specific build configurations
├── assets/         # Images, fonts, and other static assets
└── lib/
    ├── core/       # Shared utilities, themes, and global configurations
    │   ├── api/       # API clients and interceptors
    │   ├── models/    # Shared data models
    │   ├── providers/ # Global state providers
    │   ├── router/    # GoRouter configuration
    │   ├── theme/     # App themes and styling
    │   ├── utils/     # Helpers and constants
    │   └── widgets/   # Reusable UI components
    ├── features/   # Feature-specific code structured by domain
    │   ├── admin/     # Admin dashboard module
    │   ├── auth/      # Authentication module
    │   ├── parent/    # Parent dashboard module
    │   ├── student/   # Student dashboard module
    │   └── teacher/   # Teacher dashboard module
    └── main.dart   # App entry point
```

**Important Files:**
* `lib/main.dart`: Initialization of plugins (SharedPreferences), configuring Riverpod, and launching the `MindForgeApp`.
* `lib/core/utils/constants.dart`: Stores crucial app-wide constants including `apiBaseUrl`, `wsBaseUrl`, roles, storage keys, route names, and ICSE subject data.

## 4. Setup Instructions

Follow these steps to set up the project on your local machine:

**1. Clone the repository:**
```bash
git clone <your-repo-url>
cd frontend
```

**2. Install Dependencies:**
Ensure you have the Flutter SDK installed on your machine.
```bash
flutter pub get
```

**3. Generate Code (Riverpod):**
Since the project uses Riverpod with code generation, run the build runner:
```bash
dart run build_runner build --delete-conflicting-outputs
```

**4. Configure Environment Variables:**
Currently, environment constants are directly managed in `lib/core/utils/constants.dart`. Verify the following URLs before running:
* **REST API:** `https://mindforge-production-3e9b.up.railway.app/api`
* **WebSocket:** `wss://mindforge-production-3e9b.up.railway.app/ws`

## 5. Running the Project

**Local Development:**
To run the app on a connected device or emulator:
```bash
flutter run
```

**Emulator/Device Setup (Android):**
1. Open Android Studio.
2. Go to **Device Manager** and start your preferred Android Virtual Device (AVD).
3. Ensure the device is recognized by running `flutter devices`.
4. Hit `flutter run` and select your device if prompted.

## 6. Build APK

To generate an APK for Android testing or distribution, run the following commands from the root of the `frontend` folder:

**Debug APK:** (For local testing)
```bash
flutter build apk --debug
```
*Output Location:* `build/app/outputs/flutter-apk/app-debug.apk`

**Release APK:** (Optimized, ready for sharing)
```bash
flutter build apk --release
```
*Output Location:* `build/app/outputs/flutter-apk/app-release.apk`

## 7. Release Process

To generate a signed APK suitable for the Google Play Store:

1. **Create a Keystore:**
   Follow Flutter's official guide to generate a keystore file (`.jks` or `.keystore`).
   ```bash
   keytool -genkey -v -keystore c:\Users\USER_NAME\key.jks -storetype JKS -keyalg RSA -keysize 2048 -validity 10000 -alias key
   ```
2. **Configure Keystore:**
   Create a file named `key.properties` in the `android/` directory:
   ```properties
   storePassword=<password from previous step>
   keyPassword=<password from previous step>
   keyAlias=key
   storeFile=<path to the keystore file>
   ```
3. **Gradle Configuration:**
   Ensure `android/app/build.gradle` is configured to read `key.properties` and apply the signing config to the release build type.
4. **Build the Signed App Bundle:**
   ```bash
   flutter build appbundle --release
   ```

## 8. Environment & Configuration

All core configurations reside in `lib/core/utils/constants.dart`:
* **API URLs:** Managed via `AppConstants.apiBaseUrl` and `AppConstants.wsBaseUrl`. 
* **Preferences Keys:** Storage keys for JWT tokens, roles, and UI configurations.
* **Secrets:** Do not commit hardcoded secrets. If adding third-party API keys, use `.env` files with the `flutter_dotenv` package.

## 9. Workflow Explanation

**Internal Architecture:**
The app relies on a unidirectional data flow powered by **Riverpod**.
* **UI Layer:** Dumb consumer widgets that listen to providers.
* **State Layer:** Notifier providers holding app state (e.g., authentication, dashboards).
* **Data Layer:** Providers interact with API clients (built with Dio) to fetch/post data.

**Flow of Data:**
1. User triggers an action in the UI (e.g., clicks "Login").
2. UI calls a method on a Riverpod Notifier.
3. Notifier sends a request to the backend API via Dio.
4. Backend responds with data (or an error).
5. Notifier updates its state based on the response.
6. UI rebuilt automatically to reflect the new state.

## 10. Git Workflow

**Branching Strategy:**
* `main`: Production-ready code.
* `dev`: Active development and integration.
* `feature/<feature-name>`: Dedicated branches for new features.
* `bugfix/<issue-name>`: Branches for fixing bugs.

**How to create a branch:**
```bash
git checkout -b feature/teacher-dashboard
```

**How to create and merge PRs:**
1. Push your branch to the remote repository.
2. Open a Pull Request from your `feature/` branch to `dev`.
3. Request a review from another team member.
4. Once approved, squash and merge into `dev`.

## 11. Troubleshooting

* **Build Errors (`build_runner`):** If code generation fails, clear the cache and retry:
  ```bash
  flutter clean
  flutter pub get
  dart run build_runner build --delete-conflicting-outputs
  ```
* **Emulator Issues (No Internet):** In Android emulators, ensure the Wi-Fi/Cellular data is connected. Sometimes a Cold Boot from Android Studio Device Manager fixes networking issues.
* **Riverpod Lint Errors:** Ensure you are using `ConsumerWidget` or `ConsumerStatefulWidget` correctly and properly watching providers. Run `dart analyze` to catch custom lint errors.

## 12. Contribution Guide

* Keep code modular and adhere to the feature-first structure.
* Observe clean code practices. Run `flutter analyze` before committing.
* Ensure UI logic is decoupled from business logic (use Riverpod efficiently).
* Document your functions and classes where necessary.

## 13. License

Copyright © 2026 MIND FORGE. All Rights Reserved.
