---
name: rust-software-architecture
description: Design or revise the architecture of a Rust application or library when module, crate, ownership, error, or concurrency boundaries need decisions. Includes Rust CLI, web, and FFI systems (Rust 소프트웨어 아키텍처, 구조 설계).
---

# Rust software architecture

Use the actual first user flow and delivery constraints to choose boundaries. Do not prescribe a framework or a workspace layout before identifying a reason for one.

1. Trace one real request or command from its entry point through parsing, domain operation, I/O, result, and shutdown. For a library, trace one public API call. Record the owner and lifetime of each stateful value and external resource along this path.
2. Identify what must compile, version, test, or deploy independently. Start with private modules inside one crate for code organization. Use a separate crate when a compile-time dependency or public API boundary earns it. Use a Cargo workspace when multiple related packages benefit from shared development and a lockfile; a workspace is not itself a runtime or deployment boundary. Keep dependencies pointing toward stable behavior and expose only the API callers need.
3. Assign creation, mutation, sharing, and cleanup of state explicitly. Prefer borrowing or moving values where lifetimes remain clear; introduce `Arc`, locks, channels, or tasks only for actual sharing or concurrency needs. State who owns startup and shutdown, cancellation, and background work. For async web code, identify which values live across an `.await` and which tasks may outlive a request.
4. Define errors at the boundary that can act on them: keep recoverable failures as `Result`, retain useful context at I/O edges, and translate them into CLI exit behavior, HTTP responses, or library errors at the outer boundary. For batch writes, choose the commit unit and state the data left after a write failure. See the [CSV to SQLite example](README.md#csv-to-sqlite-with-partial-failure) ([한국어](README.ko.md#부분-실패와-재실행-후-db-결과)). Reserve panics for broken invariants. At an FFI edge, specify ABI, data layout, allocation/free ownership, callback lifetime and thread rules, and an explicit error and unwind policy; keep unsafe operations in a small documented wrapper.
5. Sketch the resulting module/crate graph and one concrete call path. Check that dependencies, authority to mutate state, and resource lifetimes match the graph. Verify with a focused unit test for a rule, an integration test through the public API or executable for the first flow, and a targeted concurrency or FFI boundary test when those boundaries exist. For a design request, describe expected outcomes. When implementing or reviewing executable code, run the relevant tests, using `cargo test --workspace` if the change spans workspace members, and report what actually ran.

Use the existing repository's constraints and conventions when changing code. Prefer the smallest boundary change that makes the first flow and ownership rules clear.

Official references: [Rust packages, crates, and modules](https://doc.rust-lang.org/book/ch07-00-managing-growing-projects-with-packages-crates-and-modules.html), [Cargo workspaces](https://doc.rust-lang.org/cargo/reference/workspaces.html), [ownership](https://doc.rust-lang.org/book/ch04-01-what-is-ownership.html), [recoverable errors](https://doc.rust-lang.org/book/ch09-02-recoverable-errors-with-result.html), [Send and Sync](https://doc.rust-lang.org/book/ch16-04-extensible-concurrency-sync-and-send.html), [Cargo test targets](https://doc.rust-lang.org/cargo/reference/cargo-targets.html), and [Rust FFI](https://doc.rust-lang.org/nomicon/ffi.html).

When invoked directly, read the bundled `VERSION` and report it as the declared version. Without an independent release index, do not claim the files match an official release.
