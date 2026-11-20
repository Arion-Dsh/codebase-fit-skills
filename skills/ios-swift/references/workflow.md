# iOS Swift Workflow and Verification

## Before making changes

- Read applicable `AGENTS.md` files, related source and tests, and CI. Check the working tree and preserve existing edits. Identify the workspace, project, or Swift package, then check the scheme, target, configuration, test plan, SDK, deployment target, and Swift language mode for each target. For concurrency work, check the effective default actor isolation, checking level, and relevant compiler features.
- Follow the project's Xcode, SwiftPM, and dependency locking conventions. Change deployment targets, language mode, bundle IDs, teams, signing, capabilities, entitlements, permission descriptions, or privacy settings only when the task requires them. Update inputs for generated project files and source.
- For new system permissions or capabilities, check the configuration and behavior when access is denied or revoked. Confirm each target receives its resources and configuration. Keep credentials and unredacted data out of logs and test fixtures.

## Building and running

- Start with the project's CI commands and formatting tools. Specify the workspace or project, scheme, and destination for Xcode builds and tests. Use the project's `xcodebuild -showdestinations` invocation when you need the list of valid destinations.
- Use `swift test` for packages and tests that support the host platform. Build or run targets that need the iOS SDK or runtime on the appropriate iOS destination. Record toolchain, signing, and destination failures separately.
- Follow the project's device policy. Where the project does not specify one, use an available iOS simulator or paired physical device for routine UI and interaction checks. Use a suitable physical device for hardware the simulator cannot cover, device performance, or signed installation requirements.
- If a required device or signing setup is unavailable, complete the available builds and tests and report the blocker. Preserve the project's signing identity, permissions, and compiler diagnostics during build troubleshooting.

## Verification and delivery

- Use the project's Swift Testing, XCTest, or XCUITest setup. Cover affected state, failures, cancellation, and cleanup. Prefer controllable dependencies, explicit synchronization, and bounded waits for concurrency tests.
- For changes involving navigation, view recreation, scene lifecycle, permissions, deep links, or data migration, verify the affected paths. Cover iPadOS layout, resize behavior, and accessibility as the UI changes warrant.
- Review the final diff and report changes, checks actually run, and remaining limits. For simulator or physical-device runs, record the scheme, destination, OS version, and result. Report host tests, iOS builds, simulator runs, and physical-device runs separately.

## References

- [Run an app on a simulator or physical device](https://developer.apple.com/documentation/xcode/running-your-app-on-simulated-or-physical-devices)
- [Customize Xcode build schemes](https://developer.apple.com/documentation/xcode/customizing-the-build-schemes-for-a-project)
