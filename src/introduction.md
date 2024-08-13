# Introduction

This is a high-level guide for Java developers who are
completely new to the Rust programming language. Some concepts and constructs
translate fairly well between Java and Rust, but which may be expressed
differently, whereas others are a radical departure, like memory management.
This guide provides a brief comparison and mapping of those constructs and
concepts with concise examples.

This work is essentially a fork of a similar open-source project by Microsoft: [Rust for C#/.NET Developers][rust-dotnet].

This guide is not meant to be a replacement for proper learning of the Rust language via its official documentation (for example the excellent [Rust book][rust-book]). Instead, this guide should be viewed as a resource that can help answer some questions quickly, like: _Does Rust support inheritance, interfaces, virtual threads, etc.?_ Or more generally, _how can I do X in Rust_? (based on your Java knowledge).

Assumptions:

- Reader is relatively comfortable with Java.
- Reader is completely new to Rust.

Goals:

- Provide a brief comparison and mapping of various Java topics to their
  counterparts in Rust.
- Provide links to other Rust references, books and articles for further reading on
  various topics.

Non-goals:

- Discussion of design patterns and architectures.
- Tutorial on the Rust language.
- Make reader proficient in Rust after reading this guide.
- While there are short examples that contrast Java and Rust code for some
  topics, this guide is not meant to be a cookbook of coding recipes in the
  two languages.

Here's the TLDR for a Java developer encountering Rust for the first time:


| Feature                  | Java | Rust | Note             |
| ------------------------ | ---- | ---- | ---------------- |
| Classes                  | Yes  | No   | See note 1.      |
| Interfaces               | Yes  | No   | See note 2.      |
| Enums                    | Yes  | Yes  | See note 3.      |
| Generics                 | Yes  | Yes  |                  |
| Exceptions               | Yes  | No   |                  |
| Virtual threads          | Yes  | No   |                  |
| Asynchronous Programming | No   | Yes  |                  |
| Garbage Collector        | Yes  | No   | See note 4.      |

Notes:

1. Rust has no classes. It has structures (`struct`) instead.

2. Rust has a concept similar to interfaces called `Traits`.

3. Enums in Rust are more powerful. Rust enums are most similar to [_algebraic data types_][adt] in functional languages, such as OCaml and Haskell.

4. Rust does not have a garbage collector. Memory management is accomplished through the 
   concept of _ownership_, which is arguably one of Rust's most distinctive features.

  [rust-dotnet]: https://microsoft.github.io/rust-for-dotnet-devs/latest/introduction.html
  [rust-book]: https://doc.rust-lang.org/book/
  [adt]: https://en.wikipedia.org/wiki/Algebraic_data_type
