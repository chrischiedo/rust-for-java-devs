# Testing

## Test organization

Conventionally, Java projects use separate files to host test code, irrespective of the
test framework being used and the type of tests being written (unit or integration). The test code therefore lives in a separate file (or package) from the application or library code being tested.

In Rust, it is a lot more conventional for _unit tests_ to be found in a separate test sub-module (conventionally) named `tests`, but which is placed in the same _source file_ as the application or library module code that is the subject of the
tests. One advantage of this is that the code/module and its unit tests live side-by-side.

The test sub-module is annotated with the `#[cfg(test)]` attribute, which has
the effect that the entire module is (conditionally) compiled and run only
when the `cargo test` command is issued.

Within the test sub-modules, test functions are annotated with the `#[test]`
attribute.

Integration tests are usually in a directory called `tests` that sits adjacent
to the `src` directory with the unit tests and source. `cargo test` compiles
each file in that directory as a separate crate and runs all the methods
annotated with the `#[test]` attribute. Since it is understood that integration
tests are in the `tests` directory, there is no need to mark the modules in there
with the `#[cfg(test)]` attribute.

See also:

- [Test Organization][test-org] in Rust projects.

  [test-org]: https://doc.rust-lang.org/book/ch11-03-test-organization.html

## Running tests

When using a build tool like Maven in Java, you can run tests using `mvn test`.

In Rust, you run tests by using `cargo test`.

The default behavior of `cargo test` is to run all the tests in parallel, but this can be configured to run consecutively using only a single thread:

    cargo test -- --test-threads=1

For more information, see "[Running Tests in Parallel or
Consecutively][tests-exec]".

  [tests-exec]: https://doc.rust-lang.org/book/ch11-02-running-tests.html#running-tests-in-parallel-or-consecutively


## Assertions

Java users have multiple ways to assert, depending on the framework being
used. For example, an assertion in JUnit might look like this:

```java
@Test
public void somethingIsTheRightLength() {
    String value = "something";
    assertEquals(9, value.length());
}
```

Rust does not require a separate framework or crate. The standard library
comes with built-in _macros_ that are good enough for most assertions in
tests:

- [`assert!`][assert]
- [`assert_eq!`][assert_eq]
- [`assert_ne!`][assert_ne]

Below is an example of `assert_eq!` in action:

```rust
#[test]
fn something_is_the_right_length() {
    let value = "something";
    assert_eq!(9, value.len());
}
```

  [assert]: https://doc.rust-lang.org/std/macro.assert.html
  [assert_eq]: https://doc.rust-lang.org/std/macro.assert_eq.html
  [assert_ne]: https://doc.rust-lang.org/std/macro.assert_ne.html

## Mocking

When writing tests for a Java application or library, there exist several
frameworks, like [Mockito][mockito], to mock out the dependencies. There are similar crates for Rust too, like [`mockall`][mockall], that can help with mocking. However, it is also possible to use [conditional compilation] by making use of the [`cfg` attribute][cfg-attribute] as a simple means to mocking without needing to rely on external crates or frameworks. The `cfg` attribute conditionally includes the code it annotates based on a configuration symbol, such as `test` for testing. This is not very different to using `DEBUG` to conditionally compile code specifically for debug builds. One downside of this approach is that you can only have one implementation for all tests of the module.

The example below shows mocking of a stand-alone function `var_os` from the
standard library that reads and returns the value of an environment variable. It
conditionally imports a mocked version of the `var_os` function used by
`get_env`. When built with `cargo build` or run with `cargo run`, the compiled
binary will make use of `std::env::var_os`, but `cargo test` will instead
import `tests::var_os_mock` as `var_os`, thus causing `get_env` to use the
mocked version during testing:

```rust
// Copyright (c) Microsoft Corporation. All rights reserved.
// Licensed under the MIT license.

/// Utility function to read an environment variable and return its value If
/// defined. It fails/panics if the valus is not valid Unicode.
pub fn get_env(key: &str) -> Option<String> {
    #[cfg(not(test))]                 // for regular builds...
    use std::env::var_os;             // ...import from the standard library
    #[cfg(test)]                      // for test builds...
    use tests::var_os_mock as var_os; // ...import mock from test sub-module

    let val = var_os(key);
    val.map(|s| s.to_str()     // get string slice
                 .unwrap()     // panic if not valid Unicode
                 .to_owned())  // convert to "String"
}

#[cfg(test)]
mod tests {
    use std::ffi::*;
    use super::*;

    pub(crate) fn var_os_mock(key: &str) -> Option<OsString> {
        match key {
            "FOO" => Some("BAR".into()),
            _ => None
        }
    }

    #[test]
    fn get_env_when_var_undefined_returns_none() {
        assert_eq!(None, get_env("???"));
    }

    #[test]
    fn get_env_when_var_defined_returns_some_value() {
        assert_eq!(Some("BAR".to_owned()), get_env("FOO"));
    }
}
```

  [mockito]: https://site.mockito.org/
  [mockall]: https://docs.rs/mockall/latest/mockall/
  [conditional compilation]: https://doc.rust-lang.org/reference/conditional-compilation.html
  [cfg-attribute]: https://doc.rust-lang.org/reference/conditional-compilation.html#the-cfg-attribute

## Code coverage

The Java ecosystem has several tools for analyzing test code coverage. One such popular tool is [`JaCoCo`][jacoco]. In IDEs like IntelliJ, code coverage tooling is built-in and integrated.

Rust provides [built-in code coverage implementations][built-in-cov] for
collecting test code coverage.

There are also plug-ins available for Rust to help with code coverage analysis.
It's not seamlessly integrated, but with some manual steps, developers can
analyze their code in a visual way.

The combination of [Coverage Gutters][coverage.gutters] plug-in for Visual
Studio Code and [Tarpaulin][tarpaulin] allows visual analysis of the code coverage in
Visual Studio Code. Coverage Gutters requires an LCOV file. Other tools besides Tarpaulin can be used to generate that file.

Once setup, run the following command:

```bash
cargo tarpaulin --ignore-tests --out Lcov
```

This generates an LCOV Code Coverage file. Once `Coverage Gutters: Watch` is
enabled, it will be picked up by the Coverage Gutters plug-in, which will show
in-line visual indicators about the line coverage in the source code editor.

> Note: The location of the LCOV file is essential. If a workspace (see [Project
> Structure]) with multiple packages is present and a LCOV file is generated in
> the root using `--workspace`, that is the file that is being used - even if
> there is a file present directly in the root of the package. It is quicker to
> isolate to the particular package under test rather than generating the LCOV
> file in the root.

[jacoco]: https://www.eclemma.org/jacoco/
[coverage.gutters]: https://marketplace.visualstudio.com/items?itemName=ryanluker.vscode-coverage-gutters
[tarpaulin]: https://github.com/xd009642/tarpaulin
[coverlet]: https://github.com/coverlet-coverage/coverlet
[built-in-cov]: https://doc.rust-lang.org/stable/rustc/instrument-coverage.html#test-coverage
[project structure]: ../project-structure/index.md
