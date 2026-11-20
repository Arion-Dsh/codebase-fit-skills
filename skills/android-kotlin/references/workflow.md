# Android Kotlin Workflow and Verification

## Before making changes

- Read applicable `AGENTS.md` files, source, tests, and CI configuration. Check the working tree and preserve existing edits. Identify the Gradle Wrapper, modules, build type, flavor, variant, and AGP, Kotlin, JDK, and SDK requirements. Check the NDK and ABI for JNI work.
- Use the project's toolchain, Gradle Wrapper, dependency catalog, and locking conventions. Change dependencies, signing, permissions, SDK levels, or project layout only when the task calls for it. Edit the inputs or generator behind generated code.
- Use the project's formatting, analysis, build, and test tasks for the affected variant. For an `:app` module with a `debug` variant, relevant tasks may include `:app:assembleDebug`, `:app:lintDebug`, and `:app:testDebugUnitTest`. Check the actual task names and flavor before running them.

## Device verification and ADB

- Use a device or an existing emulator that meets the project's requirements. Choose an environment that covers the hardware, performance, or operating system behavior under test.
- Run `adb devices -l` and confirm the device is online. Use `adb -s <device-serial>` to select the target for installation, launching, logs, or tests. For wireless pairing, use the address currently shown on the device.
- Prefer a project test entry point that selects the intended device. When running manually, install the app and test artifacts for the same variant. Use `adb -s <device-serial> shell am instrument -w <test-package>/<runner>` to wait for completion, then verify which tests ran and their results.
- Preserve the project's Test Orchestrator setup, runner arguments, and dependencies when running manually. Use an authorized test environment if the test clears application data. If a suitable device is unavailable, continue with available builds, static checks, and local tests, and report the device coverage.

## Verification and delivery

- Cover affected domain rules, state transitions, and failure paths with local tests. Use controllable coroutine dispatchers and time where the test tooling supports them.
- Check affected flows for UI, navigation, permissions, configuration changes, restoration, installation, upgrades, or system capabilities on an appropriate device. For UI changes, check font scaling, system bars, the software keyboard, and accessibility semantics as applicable. Test upgrades and migrations with authorized, representative old versions or data while preserving existing device data.
- Review the diff for unrelated formatting, stray generated files, credentials, and debug output. Report the changes, checks actually passed, and any gaps with reasons. For device tests, record the variant, whether it ran on a physical device or emulator, the OS version, and the result.

## References

- [Android Debug Bridge](https://developer.android.com/tools/adb)
- [Run Android tests from the command line](https://developer.android.com/studio/test/command-line)
