# Inheritance

Rust does not provide (class-based) inheritance or type hierarchies as in Java. A way to provide shared behavior between structs is via making use of _traits_. However, similar to _interface inheritance_ in Java, Rust allows defining relationships between traits by using [_supertraits_][supertrait.rs].

See also:

- [Object Oriented Programming Features of Rust][oop-rust]

[supertrait.rs]: https://doc.rust-lang.org/book/ch19-03-advanced-traits.html#using-supertraits-to-require-one-traits-functionality-within-another-trait
[oop-rust]: https://doc.rust-lang.org/book/ch17-00-oop.html
