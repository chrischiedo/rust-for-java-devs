# Documentation Comments

Java provides a mechanism to document an API using a comment syntax that uses HTML text. The JDK comes with the `javadoc` tool that can be used to compile/generate HTML output for the doc comments.

Here's a simple example of a doc comment in Java:

```java
/**
 * This is a doc comment for <b>MyClass</b>.
 *
 */
public class MyClass {}
```

In Rust, [doc comments] provide the equivalent to Java doc comments.
Documentation comments in Rust use Markdown syntax. [`rustdoc`][rustdoc] is the
documentation compiler for Rust code and is usually invoked through [`cargo
doc`][cargo doc], which compiles the comments into documentation.

Here's a simple example of a doc comment in Rust:

```rust
/// This is a doc comment for `MyStruct`.
struct MyStruct;
```


See also:

- [How to write documentation]
- [Documentation tests]

[doc comments]: https://doc.rust-lang.org/rust-by-example/meta/doc.html
[rustdoc]: https://doc.rust-lang.org/rustdoc/index.html
[cargo doc]: https://doc.rust-lang.org/cargo/commands/cargo-doc.html
[How to write documentation]: https://doc.rust-lang.org/rustdoc/how-to-write-documentation.html
[documentation tests]: https://doc.rust-lang.org/rustdoc/write-documentation/documentation-tests.html
