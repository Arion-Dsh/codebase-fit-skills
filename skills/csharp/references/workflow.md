# C# Workflow and Verification

## Before making changes

- Read applicable `AGENTS.md` files, source, tests, and CI. Check the working tree and preserve existing edits. Review `global.json`, solutions, project files, and shared MSBuild configuration. Identify the actual SDK, language version, target framework monikers (TFMs), nullable settings, build configuration, and relevant runtime identifiers (RIDs).
- Follow existing NuGet feeds, central version management, and lockfiles. For Central Package Management, change the appropriate `Directory.Packages.props`. Change dependencies and tools within the task scope and check multi-target, transitive dependency, and API compatibility.
- Follow the project's `.editorconfig`, analyzers, and formatting tools. Update the inputs for generated code. Resolve new warnings at their source and preserve nullable and analyzer settings.

## Building and verification

- For SDK-style projects, start with the repository's `dotnet build` and `dotnet test` commands. Specify the solution or project, configuration, and relevant targets. Use the project's test runner options and verify which tests ran. Follow the MSBuild, runtime, and target-platform requirements of older .NET Framework projects.
- In multi-target projects, check affected conditional compilation, frameworks, and runtimes. Verify the relevant targets when native interop, platform UI, or packaging is affected.
- When adding or changing Native AOT or trimming, or touching a target that already uses them, assess the impact. For affected targets, build the publish artifacts locally with the project's configuration and RID, review diagnostics, and run the affected paths. Include changes in reflection, serialization, dynamic loading, and dependencies in this assessment.
- Test affected public behavior, failures, cancellation, disposal, and compatibility. Use controllable clocks, explicit synchronization, and bounded waits for concurrency tests. Test database migrations with representative old data in an authorized environment.
- For Unity, use the repository's Editor version, build entry point, and Test Framework setup. Choose EditMode, PlayMode, or the target Player based on the change. Verify the target Player for changes involving IL2CPP, stripping, or native plugins. Preserve `.meta` files and GUIDs and keep unrelated assets and project files out of the diff.
- Follow the project's device and ADB policy for Android verification. If a platform, identity, toolchain, or device blocks a check, run the available checks and report their scope.

## Delivery

- Inspect the diff for generated artifacts, credentials, unrelated formatting, and dependency drift. Report the actual targets, configuration or Player platform, test results, and verification gaps.

## References

- [.NET test command](https://learn.microsoft.com/en-us/dotnet/core/tools/dotnet-test)
- [NuGet Central Package Management](https://learn.microsoft.com/en-us/nuget/consume-packages/central-package-management)
