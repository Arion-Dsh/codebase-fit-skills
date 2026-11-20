# Android Kotlin Coding Guide

## Modules, types, and errors

- Follow the existing module boundaries. Use the UI to render, navigate, and interact with the platform. Keep independently testable domain rules separate from networking, databases, and Android UI objects.
- Pass dependencies explicitly and limit visibility to actual callers. Introduce interfaces, use cases, and shared abstractions for concrete substitution boundaries or repeated behavior.
- Use nullable types for missing values, an `enum` or `sealed interface` for finite states, and a `data class` for structured data. Distinguish IDs or units with dedicated types when useful and supported by the toolchain and interop requirements.
- A `val` prevents reassignment of the reference. Check whether collections and nested objects are mutable or shared before publishing state. Expose read-only state to callers.
- Validate input at external, deserialization, and Java platform-type boundaries. Handle recoverable failures without `!!`, unsafe casts, or assertions. Preserve error distinctions that callers need and redact diagnostic data.

## UI state and lifecycle

- Give each state value a source of truth, an owner, and an update path. In screens using unidirectional data flow, the UI sends user intents and the state holder publishes observable state. A `ViewModel` can own screen-level business state. Keep transient presentation state in the relevant UI scope.
- In Compose, collect flows with `collectAsStateWithLifecycle` when supported. Use an effect appropriate to the work, with keys that identify the observed object or task. Handle recomposition, exit, and reentry.
- Tie subscriptions that update a Fragment's view to the view lifecycle, for example with `repeatOnLifecycle` in the `viewLifecycleOwner` scope. Release bindings and callbacks so they do not retain destroyed views or activities.
- Distinguish transient UI state, small restorable state, and durable data. Follow the project's use of `rememberSaveable` or `SavedStateHandle` for small values suitable for restoration. Give durable data and in-flight business operations their own recovery paths.
- Define how navigation, callback registration, and form submission handle repeats and stale results. Cover permission denials, settings changes, foreground and background transitions, and process recreation where they affect the flow.

## Coroutines, resources, and platform boundaries

- Define the owner, scope, cancellation, error propagation, and result consumer for each coroutine. Prefer the relevant lifecycle scope for work tied to a screen. Give work that must outlive it a longer-lived owner or use the project's persistent scheduling mechanism.
- Prefer structured concurrency and owned scopes. Switch dispatchers where blocking I/O or CPU-heavy work happens. Inject dispatchers when useful for tests. A `suspend` declaration alone leaves thread selection unchanged.
- Preserve `CancellationException` propagation when catching broadly or using `runCatching`. Release resources on success, failure, and cancellation. When adapting callbacks to a flow, unregister listeners and follow the source API's threading rules.
- Define synchronization, deduplication, result order, and stale-result handling. Bound retries with side effects and account for idempotency or compensation.
- Check the actual `minSdk` and runtime version guards before using new platform APIs. Cover existing data, migration, and failure recovery when changing persistence or serialization. Keep callbacks, Android lifecycle objects, and runtime handles at their respective boundaries.

## References

- [Android architecture recommendations](https://developer.android.com/topic/architecture/recommendations)
- [Android coroutine best practices](https://developer.android.com/kotlin/coroutines/coroutines-best-practices)
