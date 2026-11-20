# Rust Coding Guide

Use this guide for Rust design, implementation, review, and runtime behavior. See the [workflow guide](workflow.md) for changes, verification, and delivery.

Follow the user's request first, then the applicable `AGENTS.md` files and architecture documents, then existing interfaces, tests, and observable data behavior. Treat this guide as a fallback. Explain conflicts and their impact before changing behavior governed by a higher-priority source.

## 1. Modules and state ownership

- Give each module a clear responsibility. A function's purpose should fit in one sentence.
- Keep UI, domain rules, persistence, external I/O, platform adapters, and rendering separate.
- Use names that communicate responsibility. Avoid catchalls such as `Manager`, `Handler`, and `Utils`, and avoid objects or functions with unrelated duties.
- Extract a shared abstraction when two or more real callers need the same behavior.
- Let entry points and UI call application logic. If there is a domain layer, application logic depends on its rules. Define interfaces for external dependencies where the core needs them, implement them in adapters, and wire them up at the entry point.
- Keep windows, GPUs, input devices, databases, network access, and concrete UI objects out of the domain core.
- Keep declarations private by default. Use `pub(crate)` or `pub` for interfaces that actual callers need.
- Give mutable state one source of truth and one owner responsible for writes. Update it through a domain method, command, service, or system.
- Keep ownership explicit. Avoid mutable global singletons, `static mut`, undocumented `OnceLock` globals, and copies that create competing sources of truth.
- When using `Arc<Mutex<_>>`, `RwLock`, channels, or atomics, explain why sharing is needed and define its lifetime and thread-safety guarantees.

Review responsibilities around 40 lines for an ordinary function, 60 for a complex function, 400–500 for a source file, five parameters, or three levels of nesting. Split code when responsibilities are mixed.

## 2. Types, APIs, and ownership

- Name types and fields for their domain meaning. Avoid generic names such as `Data`, `Info`, `Manager`, and `Value` without context.
- Use newtypes to distinguish IDs, coordinates, sizes, units, times, versions, and resource keys that are easy to confuse.
- Use enums for mutually exclusive states and finite cases. Use request or options structs for complex input.
- Model domain state, IDs, and missing values explicitly. Avoid encoding them with raw strings or numbers, empty strings, sentinel numbers, or several coupled booleans.
- Use an enum or named methods when a boolean would select substantially different behavior.
- Enforce invariants in constructors, parsers, and deserialization entry points so callers can rely on validated values.
- Use `Option<T>` for possible absence and `Result<T, E>` for operations that can fail.
- Make ownership and borrowing clear. Add explicit lifetime parameters when the API needs to express a lifetime relationship.
- Review every `clone()` that copies substantial state or obscures ownership. Keep copies with a clear purpose.
- Borrow read-only input as `&T`, `&str`, or `&[T]`. Take `T` when the function assumes ownership.
- Choose collections based on ordering, uniqueness, lookup, and capacity requirements. Sort or choose an ordered collection when output must be deterministic.

### Traits, generics, and macros

- Use traits for actual substitution boundaries, shared capabilities, or protocols with multiple implementations.
- Use generics for existing operations with the same semantics. Add extension points when there is a concrete need.
- Use macros for understandable mechanical repetition. Keep their expansions amenable to formatting, debugging, and tracing.
- Keep trait objects, generics, macros, and lifetimes simple enough for callers to understand and maintain.

## 3. Control flow and errors

- Use early returns for errors, missing values, boundary cases, and invalid state.
- Break complex operations into named stages such as `validate → prepare → apply → publish`.
- Use an explicit enum state machine for multi-branch lifecycles. Avoid coupled boolean flags.
- Separate calculation from I/O so domain rules can be tested without windows, files, or a network.
- Match domain states exhaustively where they matter. Use `_` for cases the caller intentionally treats alike.
- Return recoverable failures as `Result` or `Option`, according to their semantics. Handle failures in an appropriate caller or boundary.
- Include diagnostic context such as the operation, object, ID, path, stage, or external service.
- Use domain errors that callers can match in core modules. Use aggregate errors such as `anyhow` at application boundaries.
- Avoid `unwrap()`, `expect()`, `panic!`, `unreachable!`, and `todo!` by default in application paths.
- Use `expect` in tests, static initialization, or where an invariant is demonstrably guaranteed. Make the guarantee clear.
- Define conditions, limits, and outcomes for retries, fallback, and intentionally ignored errors. Propagate errors where callers must handle them.
- Handle invalid input, overflow, empty collections, division by zero, and capacity limits when parsing, indexing, converting bounds, or performing arithmetic.

## 4. Unsafe code, concurrency, and async

- Keep application code safe by default.
- Confine necessary `unsafe` code to a minimal adapter or low-level wrapper with a safe public API.
- Put a `// SAFETY:` comment before each `unsafe` block explaining the applicable pointer, aliasing, layout, threading, and lifetime invariants.
- Resolve ownership problems with a clear design or safe API before considering `unsafe`.
- At FFI boundaries, define the ABI, ownership, threading limits, data layout, and cleanup.
- Establish the need for concurrency before choosing threads, tasks, channels, locks, or atomics.
- Define cancellation, shutdown, timeouts, backpressure, task joins, and resource cleanup.
- Keep unbounded blocking I/O and long CPU work off async executors. Account for blocking lock acquisition in async code.
- Maintain a stable lock order. Release locks before invoking callbacks, performing I/O, or calling unknown code.
- Prefer clear ownership and message-passing boundaries when shared state would require layers of `Arc<Mutex<_>>`.

## 5. Determinism, serialization, and generated code

- Produce the same rule output for the same input, version, and seed. Use an explicit random number generator and seed source.
- Give random sequences a clear owner such as a random source or scheduler. Keep random calls within that boundary.
- Keep replay, save, test, and synchronization results independent of scheduling, unordered entity traversal, wall-clock time, and hash iteration order.
- Define how to handle floating-point comparison, rounding, platform differences, and mathematical edge cases. Prefer controllable numeric representations for critical rules.
- Define version fields, defaults, and failure behavior for save and wire formats.
- Keep runtime handles, ECS entities, GPU objects, threads, temporary effects, and platform objects out of serialized data.
- Update producers, consumers, tests, migrations, and documentation when changing formats. Preserve unknown data where the contract requires it.
- Keep generated code readable, formattable, reproducible, and traceable to its inputs. Fix the generator when outputs need to change.

## 6. Review checklist

- [ ] Modules, functions, types, and state owners have clear responsibilities.
- [ ] Dependencies point in the intended direction. State stays within its ownership boundary.
- [ ] Public APIs and error types communicate the relevant invariants.
- [ ] Application paths handle recoverable errors without panicking.
- [ ] Unsafe code, concurrency, async behavior, and resource lifetimes have clear boundaries.
- [ ] Randomness, ordering, timestamps, and serialization meet the required determinism.
- [ ] Formatting and checks cover the risk of the change.
