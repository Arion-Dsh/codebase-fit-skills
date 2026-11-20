# iOS Swift Coding Guide

## Modules, types, and errors

- Follow the existing module boundaries. Keep independently testable domain rules separate from SwiftUI, UIKit, networking, and persistence. Pass dependencies explicitly, limit visibility to actual callers, and use protocols at concrete substitution boundaries.
- Prefer value semantics for data and state snapshots. Use classes when identity or reference lifetime matters. Use enums with associated values for mutually exclusive states, `Optional` for absence, and the project's `throws` or `Result` conventions for expected failures.
- Validate input at external, decoding, and Objective-C interop boundaries. Handle recoverable failures without force unwraps, `as!`, `try!`, or `fatalError`. Use `try?` when discarding error details is appropriate for the caller's recovery behavior.
- Define defaults, compatibility, and migration for `Codable`, persistence, and cross-version data. Check the deployment target before using new OS APIs and use `#available` guards and fallback behavior when needed.

## UI state and reference lifetimes

- Give each state value a source, an owner, and an update path. Follow the project's SwiftUI use of `@State`, `@Observable`, or `ObservableObject`. Distinguish creating an object, observing an externally owned object, and passing a binding. In UIKit, define ownership between view controllers, delegates, and observed objects.
- Isolate UI state and UIKit updates to the `MainActor`. Explicitly place expensive computation or blocking I/O in a suitable execution context. An `async` declaration or a `Task` created in UI code can still run on the main actor.
- Keep SwiftUI `body` declarative. Use `.task` or `.task(id:)` for asynchronous work tied to a view when appropriate and honor cancellation. Assign data and work that outlive the view to an owner with the right lifetime.
- Define strong and weak references and cleanup for closures, delegates, long-lived tasks, observation tokens, and timers. Use `unowned` only when the referenced object is guaranteed to outlive the reference. End or cancel long-running tasks at their lifecycle boundary and check for strong reference cycles with their owners.

## Swift concurrency and resources

- Prefer structured concurrency. For unstructured tasks, define the owner, when they are canceled, and where errors go. Use `Task.detached` only when the task needs independent isolation and its lifetime is defined.
- Define actor isolation and how values cross isolation boundaries. Check `Sendable` and other requirements in the target's Swift language mode. Where compatibility bridging requires `@unchecked Sendable` or a diagnostic exception, keep the exception at the boundary and document the synchronization or isolation guarantee.
- An actor can process other work while a task is suspended at `await`. If shared state matters across suspension, recheck the invariant or active request before committing a result. Honor cooperative cancellation, release resources promptly, and prevent stale results from overwriting newer state.
- Resume a continuation exactly once. Handle cancellation, duplicate callbacks, and registration failures where the source API can produce them. Clean up notifications, stream subscriptions, file handles, and other resources when they reach the end of their lifetime.

## References

- [Swift language guide to concurrency](https://docs.swift.org/swift-book/LanguageGuide/Concurrency.html)
- [Swift migration guide](https://www.swift.org/migration/documentation/migrationguide/)
- [Apple guidance on app responsiveness](https://developer.apple.com/documentation/xcode/improving-app-responsiveness)
