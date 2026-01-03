# Release Protocol & Versioning Strategy

## 1. Versioning Strategy
Automated versioning is strictly enforced for consistency between `package.json`, `app.json`, and Android build artifacts.

### Version Numbering (Semantic Versioning)
-   **Patch (x.y.Z)**: Bug fixes, small UI tweaks, minor features.
    -   Command: `npm run bump:patch`
    -   Increment: `0.0.1` -> `0.0.2`
-   **Minor (x.Y.z)**: Large refactors, significant feature sets, logic overhauls.
    -   Command: `npm run bump:minor`
    -   Increment: `0.1.0` -> `0.2.0` (Resets Patch to 0)
-   **Major (X.y.z)**: Breaking changes, complete redesigns.
    -   **Manual Update Required** in `app.json` and `package.json`.

### Build Versioning (Version Code)
-   **Source**: `app.json` (`expo.android.versionCode`)
-   **Strategy**: Auto-increment integer (+1) on EVERY `bump` command.
-   **Rule**: Never decrease or reset.

## 2. Naming Convention
Release APKs are automatically named by `android/app/build.gradle` logic:

```
Nexara-v{VersionName}-{Type}{Signed}-{Date}.apk
```
-   **Example**: `Nexara-v1.1.2-Release-Signed-20260103.apk`
-   **Type**: `Release` or `Debug`
-   **Signed**: Separated by Release config presence.

## 3. Signing Configuration
Signing keys are managed securely and injected via Gradle properties.

**Env Vars (in `gradle.properties` or System Env):**
-   `NEXARA_UPLOAD_STORE_FILE`: Path to keystore (e.g., `../promenar.keystore`)
-   `NEXARA_UPLOAD_STORE_PASSWORD`: Keystore password
-   `NEXARA_UPLOAD_KEY_ALIAS`: Key alias
-   `NEXARA_UPLOAD_KEY_PASSWORD`: Key password

**Gradle Logic (`android/app/build.gradle`):**
```groovy
signingConfigs {
    release {
        if (project.hasProperty('NEXARA_UPLOAD_STORE_FILE')) {
            storeFile file(NEXARA_UPLOAD_STORE_FILE)
            // ...
        }
    }
}
```

## 4. Release Workflow
1.  **Commit Changes**: Ensure git status is clean.
2.  **Bump Version**: Run `npm run bump:patch` (or minor).
3.  **Build**:
    -   Run: `cd android && ./gradlew assembleRelease`
    -   Artifact: `android/app/build/outputs/apk/release/*.apk`
