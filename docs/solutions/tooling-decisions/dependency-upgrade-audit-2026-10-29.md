---
module: lnk
tags: [dependencies, binrw, msrv, fuzzing]
problem_type: tooling_decision
---

# Dependency upgrade constraints

## Problem

The parser used a Git revision of binrw while the fuzz target used a different binrw release. The two crates must use the same BinRead trait. Resolving all dependencies for Rust 1.85 also selects time 0.3.45 through the optional simplelog CLI dependency, which has RUSTSEC-2026-0009.

## Resolution

Use released binrw 0.15.2 in both manifests and remove the unused fuzz-only binread dependency. Refresh both lockfiles and use num-derive 0.5.1. Rust 1.89 permits patched time and current uuid/encoding_rs releases; validate that minimum with `cargo +1.89.0 check --locked --all-targets --all-features`.

## Gotchas

Run `cargo nextest run --locked --all-features` to exercise writing as well as reading. Check the separate fuzz manifest with `cargo check --locked --manifest-path fuzz/Cargo.toml`. Windows compilation uses `cargo xwin check --locked --target x86_64-pc-windows-msvc --all-targets --all-features`; this does not execute Windows binaries. Check advisories with `cargo deny --all-features check advisories`.

The crates.io `lnk` package belongs to upstream `lilopkins/lnk-rs`. The inherited publishing job is restricted to that repository; IQON consumes fork changes by Git revision and must not publish under the upstream crate name.
