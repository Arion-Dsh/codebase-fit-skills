# C++ Coding Guide

## Modules, types, and APIs

- Organize modules and targets by responsibility. Keep public headers self-contained and expose only the declarations and dependencies callers need. Separate testable domain rules from platform, storage, and external I/O adapters.
- Prefer concrete types and composition. Use polymorphism, templates, and macros where the design has real variation or repetition. Use strong types, `enum class`, or named fields for units, IDs, distinct states, and complex inputs.
- Prefer value semantics. For borrowed data, choose references, pointers, or supported views based on nullability, mutability, and length. Define ownership transfer, retention, mutation, and threading expectations at the API boundary.

## Ownership, lifetimes, and errors

- Use RAII for memory, locks, files, sockets, and platform handles. Prefer local values or `unique_ptr` for sole ownership. Use `shared_ptr` for genuinely shared ownership and handle cycles. Pair allocation and release across library boundaries according to the API contract.
- Let member types handle resource management so the compiler can generate special member functions. For custom resource-owning types, define whether copying, moving, and assignment are supported and what is guaranteed after a move.
- Track the lifetimes of references, iterators, `span`, `string_view`, and lambda captures. Check invalidation after temporary destruction, container changes, asynchronous callbacks, and moves.
- Follow the project's exception or explicit error model. Use a supported `optional`, `expected`, or result type to distinguish absence and failure. Validate external input at runtime and use assertions for internal invariants.
- Release resources on failure, early return, and cancellation. Keep exceptions from escaping destructors and use `noexcept` according to the guarantees the code provides. Use explicit errors in targets with exceptions disabled. Translate exceptions before crossing an interface that cannot propagate them.

## Concurrency, memory safety, and interop

- Give mutable state an owner and a synchronization strategy. Identify who starts threads, tasks, and callbacks, how they stop, how they report errors, and how callers wait for completion. Release locks before unknown callbacks or slow I/O.
- Use the project's synchronization primitives to protect complete invariants. When introducing lock-free code or custom atomic memory orders, explain publication, ordering guarantees, and reclamation, and verify them.
- Check bounds, integer conversions and overflow, alignment, object lifetimes, and aliasing. Serialize using the protocol's fields, byte order, and version. Handle older or corrupted data and migrations explicitly.
- For FFI, plugins, and shared libraries, define symbols, ABI, calling conventions, layouts, encoding, ownership, and threading. Check binary compatibility before exposing C++ types across versions or toolchains. Where the project needs a stable boundary, consider a C ABI and opaque handles.
- Base performance changes on reproducible measurements. For hot paths, measure allocations, copies, data layout, and lock contention while preserving correctness and failure handling.

## References

- [C++ Core Guidelines](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines)
