# Go Coding Guide

Use this guide for Go design, implementation, review, and runtime behavior. See the [workflow guide](workflow.md) for changes, verification, and delivery.

Follow the user's request first, then the applicable `AGENTS.md` files and architecture documents, then existing interfaces, tests, and observable data behavior. Treat this guide as a fallback. Explain conflicts and their impact before changing behavior governed by a higher-priority source. Use the language and standard library available to the project.

## 1. Packages, modules, and state

- Give each package a clear responsibility. A function's purpose should fit in one sentence.
- Organize packages by domain and actual dependencies. Add architectural layers when the project needs them.
- Keep UI or protocol entry points, domain rules, storage, and external I/O separate. Make core rules testable without a network, database, or specific framework.
- Keep the package dependency graph acyclic. When an external implementation must be replaceable, define a small interface in the consuming package and implement it in the adapter.
- Use short, descriptive lowercase package names. Avoid catchall names such as `utils`, `common`, and `base`, and avoid repeating the package name in exported identifiers.
- Give each object a focused responsibility. Established names with a clear protocol meaning, such as `http.Handler`, are appropriate.
- Keep declarations unexported by default. Export only what actual callers need. Use `internal` where it fits the project structure.
- Give each mutable state object an owner and a clear way to update it. Synchronize shared writes. Multiple writers are acceptable when synchronization preserves the invariants.
- Pass configuration and dependencies explicitly. Avoid mutable globals, hidden singletons, and using `init()` to open application connections or start background tasks.
- Introduce abstractions to handle real duplication or a concrete substitution boundary.

Review responsibilities around 40 lines for an ordinary function, 60 for a complex function, 400–500 for a source file, five parameters, or three levels of nesting. Use judgment when reviewing straightforward error checks, table-driven tests, and generated code.

## 2. Types, APIs, and shared data

- Use domain terms and idiomatic Go names. Keep initialisms such as ID, URL, and HTTP consistent.
- Use defined types for IDs, units, and states that are easy to confuse, such as `type UserID string`. Type aliases share the identity of the original type.
- Model finite states with defined types and constants, and validate them at boundaries. Check switch coverage explicitly.
- Replace groups of booleans or complex arguments with a struct with descriptive fields. Use functional options when optional configuration warrants them.
- Prefer useful zero values. If the zero value cannot satisfy the invariants, provide a constructor and restrict direct updates to critical fields.
- Distinguish zero values, missing values, and failure. Use values, pointers, `(T, bool)`, or `(T, error)` according to the contract. Avoid sentinel numbers or empty strings that mean several things.
- Choose indexing, counting, and validation according to whether the protocol or domain uses bytes, Unicode code points, or user-perceived characters.
- Slices, maps, and pointers can share underlying data. Make it clear whether callers can mutate values, whether inputs are retained, and whether results need to be copied.
- Choose values or pointers based on mutability, copy cost, receiver semantics, and measurements.
- Keep a type's receiver style consistent. Once a value with a lock or other non-copyable state is in use, keep it at a stable location.
- Define the meaning of nil and empty slices and maps, especially in JSON. Initialize a map before writing to it.
- Return a genuine nil interface on success when an `error` or other interface is optional. Check for typed nil values before returning them as interfaces.

### Interfaces, generics, and reflection

- Usually put interfaces near their consumers and keep their method sets small. Reuse protocols such as `io.Reader` and `io.Writer`.
- Introduce interfaces where clients need a behavioral contract. Avoid mirroring every struct or its whole public API to support mocks.
- Return concrete types by default. Return interfaces when the API's role is to expose an abstraction.
- Use generics to share an existing algorithm or container with consistent semantics. Avoid unnecessary layers of type parameters.
- Use domain types when the schema is known. Isolate and validate `any`, `map[string]any`, reflection, or string-based dispatch at genuinely dynamic protocol boundaries.

## 3. Control flow and errors

- Use early returns to reduce nesting. Break complex operations into clear steps such as validation, preparation, execution, and commit.
- Handle errors promptly. Explain why an error can safely be ignored when using `_`, logging, or a default value to continue.
- Add useful operation and object context while keeping secrets and private data out of errors. Use `%w` when preserving error identity is part of the contract.
- Use `errors.Is` and `errors.As` to inspect wrapped errors. Branch on their identity or type.
- Introduce sentinel or custom errors when callers need to classify failures. Decide whether exposing an underlying error makes it part of the API contract before wrapping it.
- Log a failure once at the boundary responsible for handling it. Return contextual errors from inner layers.
- Return `error` for expected failures such as invalid input, network errors, or database errors. Reserve `panic`, `log.Fatal`, and `os.Exit` for their appropriate boundaries.
- Use `panic` only for an invariant violation that prevents further execution. Use `recover` at an explicit isolation boundary that handles the failure and reports it accurately.
- Set the exit code at the program entry point after cleanup. `os.Exit` bypasses deferred calls.
- Define retryable failures, retry or time limits, backoff, and cancellation. Ensure side effects are idempotent or have a compensation strategy before retrying.
- Handle index bounds, type assertions, integer conversions, overflow, division by zero, empty collections, and capacity limits. Validate external input before operations that can panic.

## 4. Context, goroutines, and synchronization

- Accept `context.Context` explicitly when an operation needs cancellation or a deadline. Usually put it first and name it `ctx`. Pass a non-nil context.
- Propagate the request context so cancellation reaches downstream calls. Give independent background tasks an application-level lifecycle.
- Pass dependencies and ordinary configuration explicitly. Store a request context in a struct only when an existing protocol requires it and its lifetime is clear.
- Call the cancel function for a cancellation or timeout context. Use context-aware operations when work must stop on cancellation.
- For every goroutine, identify what starts it, when it exits, how its errors are delivered, and how it can be awaited. Include a shutdown path for process-level workers.
- Bound concurrency, queue depth, external requests, and memory use. Limit goroutine creation when input volume is unbounded.
- Define which goroutines send, receive, and close a channel. The sender or a coordinator that knows all senders closes it after sends have stopped.
- Support cancellation for potentially blocking sends, receives, and waits when their lifetimes require it. Resolve deadlocks or leaks at the source before changing buffer sizes.
- Choose mutexes, channels, or atomics based on their semantics. Use a regular map with a lock when that fits the access pattern, and use `sync.Map` when its access model fits.
- Synchronize concurrent reads and writes to an ordinary map. Protect the full invariant, maintain a stable lock order, and release locks before calling unknown, slow, or reentrant code.
- Register work with a `WaitGroup` before waiting and record completion on every exit path. Check the minimum Go version before choosing a helper API.
- Give timers and tickers a clear stop and exit path. Check the semantics of the Go version used by the project.
- Verify concurrency changes for races. A passing race detector run covers the code paths that actually executed.

## 5. I/O, resources, and service boundaries

- Give files, network connections, response bodies, database cursors, and transactions a cleanup owner. Arrange cleanup as soon as acquisition succeeds.
- Remember that `defer` is scoped to the function. Use a small function or explicit close when resources in a long loop need prompt release.
- Handle write, flush, and commit errors. When closing can affect data integrity, handle that error too and preserve information about the original failure.
- Set HTTP timeouts and request and response size limits according to the application. Usually reuse clients and transports so requests share connection pools.
- Define HTTP methods, status codes, authentication, authorization, and input validation. Decide the status code before committing the response.
- Bind database query parameters and use cancellable APIs. Check `rows.Err()` and define transaction commit, rollback, and connection lifetime.
- Keep database and HTTP objects in adapters. Keep requests, connections, transactions, and framework contexts out of domain objects.
- Choose file replacement and durability guarantees based on the data requirements. Verify crash recovery separately when the application requires it.
- During shutdown, stop accepting work, then cancel or drain in-flight tasks and release resources within a bounded time.

## 6. Determinism, formats, and performance

- Keep deterministic output independent of map iteration, goroutine scheduling, and wall-clock time. Make randomness and time controllable for replays and tests that need stable results.
- Define JSON, save, and wire field names, missing/null/zero semantics, compatibility rules, and version markers where needed. Handle unknown fields according to the protocol.
- Update producers, consumers, tests, and migrations when a format changes. Preserve numeric precision and any data the protocol requires consumers to retain.
- Keep mutexes, channels, connections, runtime handles, and other ephemeral resources out of serialized data.
- Measure before optimizing. Use benchmarks, allocation data, or profiles before making performance claims or adding pools and complex caches.
- Avoid introducing `unsafe` or cgo by default. When necessary, confine them to a minimal adapter, document memory and pointer retention, threading, and release rules, and provide a safe API.
- Make generated code reproducible from identifiable inputs and tools. Fix the source or generator when outputs need to change.

## 7. Review checklist

- [ ] Packages have clear responsibilities and dependencies. Exports and interfaces serve actual callers.
- [ ] Types, zero values, nil, and shared-data semantics are clear.
- [ ] Errors can be traced and classified. Failure paths are visible.
- [ ] Context propagates correctly. Goroutines can exit. Concurrency and queues are bounded.
- [ ] Locks, channels, resources, and transactions have correct lifetimes.
- [ ] Protocol compatibility, determinism, and performance requirements have supporting evidence.
- [ ] Formatting and checks cover the risk of the change.

## References

- [Effective Go](https://go.dev/doc/effective_go) covers foundational idioms. Check newer language features separately.
- [Go Code Review Comments](https://go.dev/wiki/CodeReviewComments) covers review conventions.
- [The context package](https://pkg.go.dev/context) documents cancellation, deadlines, and calling conventions.
- [Data Race Detector](https://go.dev/doc/articles/race_detector) covers race checks and their scope.
