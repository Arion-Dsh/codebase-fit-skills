# C# Coding Guide

## Modules, types, and errors

- Follow the project's assembly boundaries and responsibilities. Pass dependencies explicitly and limit visibility to actual callers. Define interfaces where implementations need to be replaceable. Separate domain rules from framework entry points, storage, and external I/O.
- Model IDs, units, and finite states with domain types. Choose a class, struct, or supported record type based on identity, equality, and copy semantics. Read-only interfaces and records can still hold references to mutable objects.
- Follow the project's nullable settings. Where nullable reference types are enabled, use `T?` and non-nullable declarations to express the contract. Validate external input, deserialized data, and values from legacy assemblies at runtime. Use `!` only when you can establish why a value is non-null.
- Model expected absence with nullable types, `TryXxx`, or the project's result types. Catch exceptions where they can be handled, translated, or given useful context. Use `throw;` to rethrow the current exception while preserving its stack trace. Avoid duplicate logs and redact private data.

## Async, cancellation, and resources

- Return `Task` or `Task<T>` for awaitable operations according to project conventions. Use other return types when their semantics are needed. Reserve `async void` for event interfaces that require it and handle errors there. Avoid synchronously blocking on async work. Identify who owns background tasks, how they stop, and how errors surface.
- Pass `CancellationToken` through cancellable calls. Distinguish caller cancellation, timeouts, and other errors. Check cancellation at appropriate points in long work or loops. Define what happens to side effects already committed when cancellation arrives.
- Update UI state on the thread required by the platform. Define who writes shared state and how writers synchronize. Bound concurrent work, queues, and external calls according to load. Prevent stale results from overwriting newer state.
- Define disposal responsibility for resource creators, consumers, and dependency injection containers. Use `using` or `await using` for owned resources and cover exceptions and cancellation. Unsubscribe from events, dispose timers and observations, and stop background work when its owner ends.
- Keep long-lived objects from directly capturing shorter-lived services. Define when async enumeration and deferred queries run and release their resources. Finish enumeration before disposing its data source.

## Interop and Unity

- Keep P/Invoke, `unsafe`, and native callbacks in an interop layer. Define the ABI, encoding, layout, ownership, callback lifetime, and threading. Where supported, use `SafeHandle` for native handles owned by managed code.
- For serialization, wire DTOs, and persistence changes, check field contracts, null handling, existing data, and migration. For targets using Native AOT or trimming, assess affected serialization, reflection, dynamic loading, and dependencies, then verify under the target configuration.
- In Unity, assign state and cleanup to the appropriate scene, GameObject, or component. Follow the project's Unity lifecycle, main-thread, and serialization rules. Account for the null semantics of destroyed `UnityEngine.Object` instances and preserve scene and prefab data when renaming serialized fields.

## References

- [C# nullable reference types](https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/null-safety/nullable-reference-types)
- [C# async return types](https://learn.microsoft.com/en-us/dotnet/csharp/asynchronous-programming/async-return-types)
- [.NET native interop best practices](https://learn.microsoft.com/en-us/dotnet/standard/native-interop/best-practices)
- [UnityEngine.Object](https://docs.unity.com/en-us/engine/6000.0/script-reference/unityengine/object)
