# Rust Workflow and Verification

Use this guide for Rust and Cargo changes, verification, and delivery. See the [coding guide](code.md) for design and implementation.

## 1. Before making changes

- Read applicable `AGENTS.md` files, relevant source and tests, configuration, and project documentation.
- Identify the crates, workspace, features, dependency directions, and actual callers.
- Check the working tree and preserve existing edits.
- Define the expected behavior, boundaries, failure paths, acceptance criteria, and minimum verification scope.
- Proceed when a safe default is available and state material assumptions in the delivery summary.
- Proceed within existing authorization. For a consequential open decision about public APIs, data formats, migrations, performance, or user behavior, describe a concrete proposal and its impact before asking for confirmation.

## 2. Change scope and authorization

- Change only the files and paths needed for the task. Fix the underlying cause.
- Prefer `rg` or `rg --files` for search and keep edits reviewable.
- Keep unrelated upgrades, refactors, history changes, and cleanup of user files outside the task.
- Remove obsolete code, duplicate writes, or compatibility layers made unnecessary by the change where safe and relevant.
- Preserve user edits and history. Avoid destructive uses of `git reset`, `git checkout`, or force push.
- Ensure authorization covers deleting, overwriting, or migrating external data, changing dependencies outside the repository, or expanding permissions. Check the exact scope before acting.
- Ensure authorization covers commits, pushes, releases, deployments, messages, and changes to external systems.

## 3. I/O, resources, and security

- Keep windows, textures, GPU objects, file handles, network connections, and HUD or rendering entities out of domain models.
- Open, validate, use, and release external resources in the appropriate boundary layer. Cover success, failure, cancellation, and early returns.
- Keep paths, environment variables, configuration, and resource keys centralized.
- Validate external input against applicable size, range, encoding, version, permission, depth, and count limits.
- Keep keys, tokens, signing certificates, private configuration, and unrelated user data out of reads, output, commits, and uploads unless the authorized task requires access. Redact diagnostic data.
- Keep credentials, personal data, complete save data, and unredacted request content out of logs.
- Preserve relevant lint rules, tests, validation, and permissions when diagnosing failures.
- Before adding a dependency, assess its necessity, maintenance, license, feature impact, build cost, and existing alternatives.

## 4. Comments, documentation, and naming

- Explain reasoning, invariants, or decisions that the code cannot show directly.
- Document public APIs and relevant ownership, lifetimes, threading limits, platform differences, and safety boundaries.
- Update documentation when behavior, configuration, formats, public APIs, or error semantics change.
- Give TODOs a reason and a completion condition. Include the impact when it helps prioritize the work.

```text
TODO(issue-or-owner): reason, impact, and what must happen to complete the work
```

## 5. Testing and verification

### Test design

- Name tests for conditions and expected outcomes. Make setup, execution, and assertions easy to identify.
- Verify one main behavior per test. Favor public behavior, domain invariants, and failure paths.
- When practical, add a regression test that reproduces the defect being fixed.
- Use stable, minimal, redacted data. Record the seed for randomized tests and include enough context when they fail.
- Verify cleanup and repeated execution where resources, threads, tasks, locks, files, connections, or GPU objects are involved.
- Use snapshots when a large output is deterministic. Assert important behavior directly.

### Rust checks

Start with the repository's CI commands. Choose checks for the affected packages, toolchains, compilation targets, and feature combinations. Use these common commands as appropriate.

```text
cargo fmt --check
cargo check
cargo clippy
cargo test
```

- Cover affected combinations the project supports, including default, minimal, or selected features where relevant. Check mutually exclusive features separately and use `--all-features` only for supported combinations.
- Select `--all-targets` based on the impact of the change. Apply `-D warnings` according to the project's diagnostic and CI policy.
- Add relevant integration tests, examples, benchmarks, data migrations, generator checks, cleanup checks, or UI and rendering verification. Report cross-compilation and execution on the target separately.

For checks you could not run, record the blocker and the relevant command.

## 6. Delivery

- Summarize changes, changed files, checks actually run, and remaining verification, with reasons.
- Claim only the commands, tests, builds, and manual checks you performed.
- Review the diff for temporary files, debug output, credentials, stray generated files, and unrelated formatting.
- Preserve user edits. Create commits, push, or rewrite history only within the requested scope.

## 7. Completion checklist

- [ ] Changes address the request and avoid unrelated refactors.
- [ ] Formatting, builds, analysis, and tests cover the risk of the change.
- [ ] Failure, cleanup, and determinism were checked or any verification limits were recorded.
- [ ] Documentation, configuration, tests, and implementation agree.
- [ ] The diff contains no secrets, temporary files, or unrelated changes.
