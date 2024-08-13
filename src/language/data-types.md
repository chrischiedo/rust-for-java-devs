# Data Types

The following table lists the primitive types in Rust and their equivalent in Java:

| Rust    | Java      | Java Wrapper Class     | Note             |
| ------- | --------- | ---------------------- | ---------------- |
| `bool`  | `boolean` | `Boolean`              |                  |
| `char`  | `char`    | `Character`            | See note 1.      |
| `i8`    | `byte`    | `Byte`                 |                  |
| `i16`   | `short`   | `Short`                |                  |
| `i32`   | `int`     | `Integer`              | See note 2.      |
| `i64`   | `long`    | `Long`                 |                  |
| `i128`  |           |                        |                  |
| `isize` |           |                        | See note 3.      |
| `u8`    |           |                        |                  |
| `u16`   |           |                        |                  |
| `u32`   |           |                        |                  |
| `u64`   |           |                        |                  |
| `u128`  |           |                        |                  |
| `usize` |           |                        | See note 3.      |
| `f32`   | `float`   | `Float`                |                  |
| `f64`   | `double`  | `Double`               |                  |
| `()`    | `void`    | `Void`                 | See note 4.      |


Notes:

1. [`char`][char.rs] in Rust and [`Character`][Character.java] in JVM have different
   definitions. In Rust, a `char` is 4 bytes wide that is a [Unicode scalar
   value], but in the Java Platform, a `Character` is 2 bytes wide (16-bit fixed-width) and stores the character
   using the UTF-16 encoding. For more information, see the [Rust `char`
   documentation][char.rs].

2. Unlike Java (and like C/C++/C#), Rust makes a distinction between signed and unsigned
   integer types. While all integral types in Java represent _signed_ numbers, Rust provides both _signed_ (e.g. `i32`) and _unsigned_ (e.g. `u32`) integer types.

3. Rust also provides the `isize` and `usize` types that depend on the architecture of the
   machine your program is running on: 64 bits if you're on a 64-bit architecture and 32 bits
   if you're on a 32-bit architecture.

4. While a unit `()` (an empty tuple) in Rust is an _expressible value_, the
   closest cousin in Java would be `void` to represent nothing.


See also:

- [Primitives (Rust By Example)][primitives.rs]

[char.rs]: https://doc.rust-lang.org/std/primitive.char.html
[Character.java]: https://docs.oracle.com/javase/8/docs/api/java/lang/Character.html
[Unicode scalar value]: https://www.unicode.org/glossary/#unicode_scalar_value
[primitives.rs]: https://doc.rust-lang.org/rust-by-example/primitives.html
