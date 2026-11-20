# Codebase Fit

Language-specific [Agent Skills](https://agentskills.io/specification) that help coding agents follow your codebase's conventions and verify changes with the project's own tools.

- **Fit the codebase.** Follow its architecture, versions, and conventions. Reuse existing interfaces and patterns where they fit.
- **Keep changes focused.** Fix the underlying cause, use clear names and types, and explain only what the code cannot show.
- **Verify what changed.** Choose checks for the affected target and behavior. Report what ran and what remains unverified.

## Skills

| Skill | Covers |
| --- | --- |
| [`go`](skills/go/SKILL.md) | Go APIs, error handling, concurrency, modules, builds, and tests |
| [`rust`](skills/rust/SKILL.md) | Rust ownership, errors, concurrency, unsafe code, FFI, and Cargo |
| [`android-kotlin`](skills/android-kotlin/SKILL.md) | Native Android with Kotlin, Compose or Views, coroutines, Gradle, and device testing |
| [`ios-swift`](skills/ios-swift/SKILL.md) | Native iOS and iPadOS with SwiftUI or UIKit, Swift concurrency, and Xcode |
| [`cpp`](skills/cpp/SKILL.md) | C++ ownership, lifetimes, ABI, native builds, and diagnostics |
| [`csharp`](skills/csharp/SKILL.md) | C# across .NET, .NET Framework, and Unity |

## Install

Run these commands from the project where you want to use the skills. The [Skills CLI](https://github.com/vercel-labs/skills) uses Node.js and supports Codex, Claude Code, Cursor, OpenCode, and [other agents](https://github.com/vercel-labs/skills/blob/main/docs/agents.md).

```sh
# See the skills available in the repository.
npx skills add https://github.com/Arion-Dsh/codebase-fit-skills --list

# Install all six skills into the current project for these agents.
npx skills add https://github.com/Arion-Dsh/codebase-fit-skills --skill '*' -a codex -a claude-code -a cursor -a opencode

# Install one skill for a single agent across projects.
npx skills add https://github.com/Arion-Dsh/codebase-fit-skills --skill rust -a codex -g
```

## Use

Ask your agent to use the relevant skill for the task. The `SKILL.md` entry points to `references/code.md` for coding and review, and to `references/workflow.md` for changes, builds, tests, and delivery. In mixed-language repositories, apply each skill to the affected language or platform. Follow the repository's own architecture, toolchain, and testing requirements.

## License

Licensed under the [MIT License](LICENSE).
