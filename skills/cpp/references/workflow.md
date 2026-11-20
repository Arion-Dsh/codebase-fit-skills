# C++ Workflow and Verification

## Before making changes

- Read applicable `AGENTS.md` files, source, tests, build settings, and CI. Check the working tree and preserve existing edits. Identify affected targets, compilers, standard libraries, C++ standards, architectures, configurations, and platform toolchains.
- Use the project's CMake, MSBuild, Bazel, Meson, or other build entry point. Follow its settings for exceptions, RTTI, definitions, linkage, and runtime libraries. Change global build options, dependencies, or ABI settings only when the task requires it.
- Follow the project's dependency manifests, lockfiles, toolchain files, and versioning conventions. Review platform support, licensing, transitive dependencies, and build impact before adding dependencies. Fix generated code at its source or generator.

## Building, testing, and diagnostics

- Start with the project's CI commands. For CMake Presets, use the appropriate `cmake --preset <configure-preset>`, `cmake --build --preset <build-preset>`, and `ctest --preset <test-preset>`. Check each preset and select the intended configuration, such as Debug or Release, for multi-configuration generators.
- Use the project's clang-format, clang-tidy, compiler warning policy, and test framework. Check targets, definitions, and the compilation database so analysis and tests cover the intended configuration. Verify compilation, linking, and execution as applicable.
- Consider ASan and UBSan for changes involving lifetimes or memory boundaries on supported targets. Use a separate TSan configuration for relevant shared-state or concurrency changes. Report the actual toolchain, configuration, and paths exercised.
- For cross-platform or ABI changes, cover affected architectures, preprocessor branches, shared-library loading, and representative callers. When the project promises ABI compatibility, link or load the new library with an already-compiled caller from a previous release and exercise key calls. Report cross-compilation and execution on the target separately. Follow the project's device and ADB policy for Android verification.
- Cover relevant boundary values, exception or error paths, cancellation, destruction, and repeated execution. Use representative old data when formats change. Record build mode, machine, and inputs for reproducible performance comparisons.

## Delivery

- Inspect the diff for generated files, build artifacts, credentials, unrelated formatting, and toolchain drift. Preserve user edits. Report the actual build targets, test results, and verification limits.

## References

- [CMake Presets](https://cmake.org/cmake/help/latest/manual/cmake-presets.7.html)
- [AddressSanitizer](https://clang.llvm.org/docs/AddressSanitizer.html)
- [UndefinedBehaviorSanitizer](https://clang.llvm.org/docs/UndefinedBehaviorSanitizer.html)
- [ThreadSanitizer](https://clang.llvm.org/docs/ThreadSanitizer.html)
