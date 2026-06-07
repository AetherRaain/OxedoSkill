# Systems programming — polyglot reference (load only in Rust/Go/C/C++ projects)

**Dormant by default.** For a *future / sibling* systems project, not OxedoScans. Load when working in
`.rs`/`.go`/`.c`/`.cpp` files. The three iron laws in `SKILL.md` (TDD, root-cause debugging,
verify-before-claims) still apply.

Distilled from wshobson `systems-programming` plugin (agents `rust-pro`, `golang-pro`, `cpp-pro`,
`c-pro` + skills: memory-safety-patterns, go-concurrency-patterns, rust-async-patterns) and jeffallan
`rust-engineer`/`golang-pro`/`cpp-pro`/`embedded-systems`.

## Memory safety (the core systems concern)

| Bug | Cause | Prevention |
|---|---|---|
| Use-after-free | Access freed memory | Ownership, RAII, lifetimes |
| Double-free | Free twice | Smart pointers, single owner |
| Leak | Never freed | RAII, scope-bound lifetimes |
| Buffer overflow | Write past end | Bounds checking, slices |
| Dangling pointer | Points to freed memory | Lifetime tracking |
| Data race | Unsynchronized concurrent access | Ownership, `Send`/`Sync`, mutexes |

- **RAII everywhere**: tie resource lifetime to object scope. Rust enforces it; C++ uses
  destructors + smart pointers; C requires disciplined paired alloc/free with `goto cleanup`.
- Prefer the language's safe abstractions over raw pointers/manual memory. Reach for `unsafe`/raw only
  with a clear, documented invariant — and isolate it behind a safe API.

## Rust (1.75+)

- **Ownership & borrowing** is the model: one owner, borrow with `&`/`&mut`, the borrow checker proves
  safety at compile time. Don't fight it with `clone()` everywhere — model ownership correctly.
- Smart pointers: `Box` (heap), `Rc`/`Arc` (shared, Arc = thread-safe), `RefCell`/`Mutex`/`RwLock`
  (interior mutability). `Arc<Mutex<T>>` for shared mutable across threads.
- **Error handling**: `Result<T, E>` + `?` operator; custom error enums (`thiserror`), `anyhow` for apps.
  No `unwrap()` in production paths — handle or propagate.
- **Async**: Tokio runtime; `async`/`await`, streams, `select!`. `axum` for web services.
- Zero-cost abstractions, traits + generics (and GATs) for polymorphism without runtime cost.
- Verify gate: `cargo build`, `cargo clippy -- -D warnings`, `cargo test`.

## Go (1.21+)

- **Concurrency**: goroutines + channels. Patterns: worker pool, fan-in/fan-out, pipeline. `select`
  for multiplexing/non-blocking. Always have a way to stop goroutines — leaks are the classic bug.
- **Context** (`context.Context`) threads cancellation/timeouts/deadlines through call chains; honor it
  for graceful shutdown. Pass as the first arg, never store in a struct.
- `sync` primitives (Mutex, RWMutex, WaitGroup, Once) for shared state; prefer channels for ownership
  transfer, mutexes for protecting state.
- **Error handling**: explicit `if err != nil`, wrap with `fmt.Errorf("...: %w", err)`, inspect with
  `errors.Is`/`errors.As`. Generics for type-safe reusable code.
- Run the race detector: `go test -race`. Verify gate: `go build ./...`, `go vet`, `go test -race ./...`.

## C / C++

- **C++**: modern C++17/20 — RAII + smart pointers (`unique_ptr`, `shared_ptr`), no raw `new`/`delete`
  in app code. `std::` containers/algorithms over hand-rolled. Move semantics for ownership transfer.
  Rule of zero/five. `const`-correctness.
- **C**: disciplined ownership conventions (who frees what, documented), paired alloc/free,
  `goto cleanup` for error paths, bounds-checked buffer handling. Initialize everything.
- Tooling: sanitizers (`-fsanitize=address,undefined`), valgrind, clang-tidy, static analyzers.
  Verify gate: build with `-Wall -Wextra -Werror` + run under ASan/UBSan in tests.

## General systems discipline

- Measure, don't guess: profile (perf, flamegraphs) before optimizing. Mind cache locality and
  allocation in hot loops.
- Concurrency: minimize shared mutable state; make ownership explicit; test under race detectors.
- Keep `unsafe`/FFI boundaries small, documented, and wrapped in safe interfaces.
