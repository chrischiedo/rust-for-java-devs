# Annotations

In Java, annotations are a specialized kind of interface that allows you to add custom metadata about your code.

Here's an example of a method that's annotated with `@Test` to indicate that it's a test method:

```java
@Test
public void someTestMethod() {
    // arrange
    // act
    // assert
}
```

The equivalent of annotations in Rust are called _attributes_. Here's a test method that is annotated with the `#[test]` attribute:

```rust
#[test]
fn some_test_method() {
    // arrange
    // act
    // assert
}
```

See also:

- [Attributes]

[Attributes]: https://doc.rust-lang.org/rust-by-example/attribute.html

