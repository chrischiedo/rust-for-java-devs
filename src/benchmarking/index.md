# Benchmarking

Running benchmarks in Rust is done via [`cargo bench`][cargo-bench], a specific
command for `cargo` which executes all the methods annotated with the
`#[bench]` attribute. This attribute is currently [unstable][bench-unstable] and
available only for the nightly channel.

Java users can make use of the `Java Microbenchmark Harness (JMH)` tool to write microbenchmarks for their Java code. The equivalent of `JMH` for Rust is a crate named
`Criterion`.

As per its [documentation][criterion-docs], `Criterion` collects and stores
statistical information from run to run and can automatically detect performance
regressions as well as measuring optimizations.

With `Criterion`, it is possible to use the `#[bench]` attribute without moving to
the nightly channel.

It is possible to integrate benchmark results with the [GitHub Action for Continuous Benchmarking][gh-action-bench]. `Criterion`, in fact, supports multiple output formats, amongst which there is also the `bencher` format, mimicking the nightly `libtest` benchmarks and compatible with the mentioned action.

[cargo-bench]: https://doc.rust-lang.org/cargo/commands/cargo-bench.html
[bench-unstable]: https://doc.rust-lang.org/rustc/tests/index.html#test-attributes
[criterion-docs]: https://bheisler.github.io/criterion.rs/book/index.html
[gh-action-bench]: https://github.com/benchmark-action/github-action-benchmark
