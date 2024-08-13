# Polymorphism

Rust does not support classes and sub-classing; therefore polymorphism can't be
achieved in an identical manner to Java.

In Rust, polymorphism is mainly achieved via _virtual dispatch_ using [trait objects].

See also:

- [Generics]
- [Inheritance]

[generics]: ./generics.md
[inheritance]: ./inheritance.md
[trait objects]: https://doc.rust-lang.org/book/ch17-02-trait-objects.html#using-trait-objects-that-allow-for-values-of-different-types