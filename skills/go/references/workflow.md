# Go Workflow and Verification

Use this guide for Go changes, verification, and delivery. See the [coding guide](code.md) for design and implementation.

Follow the user's request first, then the applicable `AGENTS.md` files and architecture documents, then existing interfaces, tests, and observable data behavior. Treat this guide as a fallback. Proceed within existing authorization. Ask about consequential decisions that fall outside that scope, are irreversible, or cannot be reasonably inferred.

## 1. Before making changes

- Read applicable `AGENTS.md` files, related source and tests, configuration, and architecture documents.
- Check the working tree and preserve existing edits. Identify the behavior, boundaries, failure cases, and acceptance criteria.
- Check `go.mod`, `go.work` where present, the Go and toolchain versions, module boundaries, build tags, target platforms, cgo, and CI commands.
- Follow the project's layout, framework, and tooling.
- When a safe default is available, proceed and report material assumptions. Clarify changes whose impact is genuinely unclear.

## 2. Change scope and authorization

- Change only the files and paths needed for the task. Fix the underlying cause.
- Prefer `rg` or `rg --files` for search and keep edits reviewable.
- Keep dependency and toolchain upgrades, unrelated refactors, and cleanup of user files outside routine changes.
- Remove obsolete code, duplicate state, or compatibility layers made unnecessary by the change where safe and relevant.
- Preserve user edits and history. Avoid destructive Git operations and force pushes.
- Keep public API, protocol, migration, and user-facing changes within the approved scope. Present concrete options and their impact when a consequential decision remains open.
- Ensure authorization covers commits, pushes, releases, deployments, external messages, or other changes to external systems. Use existing authorization.
- Keep unrelated private data and credentials out of reads, output, commits, and uploads. Redact logs and test data.

## 3. Modules, dependencies, and generated files

- Before adding a dependency, check the standard library and current dependencies. Consider maintenance, licensing, compatibility, transitive dependencies, and build impact.
- Changes to imports or dependencies can affect `go.mod` and `go.sum`, including imports used only by tests, tools, platform-specific files, or build tags. Run `go mod tidy` with the project's toolchain when synchronization is needed and inspect the complete diff. On Go 1.23 or later, use `go mod tidy -diff` for a check without editing the module files.
- Keep the existing `go`, `toolchain`, and `replace` directives, vendor setup, and `go.work` unless a change requires adjusting them. Evaluate upgrades against the cause of the problem.
- Identify the validation scope of each affected module. A root-level `go test ./...` may miss other modules.
- Resolve the underlying failure before considering changes to global Go settings, validation, linting, or permissions.
- Follow the existing proxy and credential setup for private modules. Keep module information and tokens out of unrelated services and logs.
- Before running `go generate`, read the relevant directives and check the tools, file writes, and other effects they trigger.
- Use the generators and versions chosen by the project. Inspect generated diffs and keep the inputs and outputs consistent.

## 4. Comments, documentation, and diagnostics

- Use comments to explain reasoning, invariants, ownership, and tradeoffs that the code cannot show directly.
- Document exported API behavior, errors, nil and zero values, sharing, concurrency safety, and relevant lifetimes.
- Update documentation and examples when behavior, configuration, protocols, public APIs, or error semantics change.
- Give TODOs a reason and a completion condition. A useful form is `TODO(issue-or-owner): reason and completion condition`.
- Include relevant operations and objects in diagnostics. Keep credentials, full private payloads, and unredacted personal data out of logs.

## 5. Test design

- Name tests for their conditions and outcomes. Favor public behavior, invariants, and failure paths over assertions tied to internals.
- Use table-driven tests and subtests when cases share a structure.
- When the benefit justifies it, add a regression test that reproduces the defect being fixed.
- Use minimal, stable, redacted data. Isolate resources with temporary directories, cleanup functions, and local test servers.
- Avoid making tests depend on the public internet, production databases, conflicting ports, or fixed sleeps. Use explicit synchronization and bounded waits for concurrency tests.
- Check global state, environment variables, and shared resources before using `t.Parallel()`. Coordinate changes to process-wide state.
- Report worker goroutine errors to the test coordinator. Call `Fatal` or `FailNow` only from the goroutine that runs the test.
- Cover cancellation, timeouts, repeated close, partial failure, transaction rollback, and worker shutdown in proportion to risk.
- Use fuzz tests where they help exercise parsers, encoders, and complex boundaries. Use benchmarks for performance claims and assert behavior directly.

## 6. Formatting, building, and verification

Start with the project's CI commands. Format only handwritten Go files touched by the change. Use the project's configuration and tool versions for `goimports`, Staticcheck, and golangci-lint.

For a single module, choose the relevant checks and add any required by the project.

```text
gofmt -l <changed Go files>
go build ./...
go vet ./...
go test ./...
```

- Replace the placeholder with actual filenames and quote paths with spaces. A clean `gofmt -l` run prints no filenames, even though the exit status alone is insufficient.
- Use `go build` for build verification, `go vet` for suspicious code, and `go test` for behavior.
- For concurrency, shared state, or service lifecycle changes, run `go test -race ./...` or a focused equivalent where supported.
- Report toolchain, platform, or cost limits on race checks. Claim race verification only for runs with the detector enabled.
- Use `-count=1` when a fresh test run is required. Follow project conventions for integration tests, database tests, and build tags.
- Extend the verification matrix for platform-specific files, cgo, build tags, and multiple modules. Report cross-compilation and execution on the target separately.
- Give fuzz and benchmark runs a specific package, target, and budget. Compare performance with consistent inputs and environments.
- Follow project policy for checks such as `govulncheck` when dependencies or security-relevant code change. Report tooling and network limitations.
- Use a proportionate check for documentation-only and other low-risk changes. Expand checks when new findings warrant it.

## 7. Delivery

- Inspect the final diff for unrelated formatting, temporary files, debug output, credentials, and dependency drift.
- Summarize changes, files, checks actually run, and material risks or limitations.
- For required checks you could not run, give the reason and the command that would complete verification. Claim only results you observed.
- Use output or other evidence to distinguish regressions from pre-existing repository or environment problems.
- Preserve user edits. Create commits, push, or rewrite history only within the requested scope.

## 8. Completion checklist

- [ ] Changes address the request, preserve existing work, and avoid unrelated refactors.
- [ ] Formatting, builds, analysis, and tests cover the actual risk and project requirements.
- [ ] Required cancellation, error, cleanup, concurrency, and compatibility paths were checked.
- [ ] Source, generated files, dependencies, tests, and documentation agree.
- [ ] The diff and delivery summary match the checks actually performed.

## References

- [Go command documentation](https://pkg.go.dev/cmd/go) covers modules, builds, tests, and tooling.
- [Data Race Detector](https://go.dev/doc/articles/race_detector) covers race checks and their scope.
