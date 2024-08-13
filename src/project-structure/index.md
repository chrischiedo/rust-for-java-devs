# Project Structure

In Java, a standard Maven project would have the following structure:


    project directory/
    .
    +-- src/
    |  +-- main/
    |    +-- java/
    |     +-- Application.java
    |    +-- resources
    |     +-- application.properties
    |  +-- test/
    |    +-- java/
    |     +-- ApplicationTests.java
    +-- target/
    |   +-- build output files
    +-- mvnw
    +-- mvnw.cmd
    +-- pom.xml

- Both Java source code and the corresponding test files are contained in the `src/` directory.
- The `pom.xml` file and the Maven wrappers are stored at the root of the project.
- The `target` directory contains all the build output files.

Cargo uses the following conventions for the [package layout] to make it easy to
dive into a new Cargo [package][rust-package]:

    project directory/
    .
    +-- Cargo.lock
    +-- Cargo.toml
    +-- src/
    |   +-- lib.rs
    |   +-- main.rs
    +-- benches/
    |   +-- some-bench.rs
    +-- examples/
    |   +-- some-example.rs
    +-- tests/
        +-- some-integration-test.rs
    +-- target/
    |   +-- build output files

- `Cargo.toml` and `Cargo.lock` are stored in the root of the package.
- `src/lib.rs` is the default library file, and `src/main.rs` is the default
  executable file (see [target auto-discovery]).
- Benchmarks go in the `benches` directory, integration tests go in the `tests`
  directory (see [testing][section-testing],
  [benchmarking][section-benchmarking]).
- Examples go in the `examples` directory.
- There is no separate crate for unit tests, unit tests live in the same file as
  the code (see [testing][section-testing]).

[package layout]: https://doc.rust-lang.org/cargo/guide/project-layout.html
[rust-package]: https://doc.rust-lang.org/cargo/appendix/glossary.html#package
[target auto-discovery]: https://doc.rust-lang.org/cargo/reference/cargo-targets.html#target-auto-discovery
[section-testing]: ../testing/index.md
[section-benchmarking]: ../benchmarking/index.md

## Managing large projects

For very large projects in Rust, Cargo offers [workspaces][cargo-workspaces] to
organize the project. A workspace can help manage multiple related packages that
are developed in tandem. Some projects use [_virtual
manifests_][cargo-virtual-manifest], especially when there is no primary
package.

[cargo-workspaces]: https://doc.rust-lang.org/book/ch14-03-cargo-workspaces.html
[cargo-virtual-manifest]: https://doc.rust-lang.org/cargo/reference/workspaces.html#virtual-workspace
