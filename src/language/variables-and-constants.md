# Variables and Constants

## Variables

Consider the following example around variable assignment in Java:

```java
int x = 5;
```

And the same in Rust:

```rust
let x: i32 = 5;
```

So far, the only visible difference between the two languages is that the
position of the type declaration is different. Also, both Java and Rust are
type-safe: the compiler guarantees that the value stored in a variable is always
of the designated type. The example can be simplified by using the compiler's
ability to automatically infer the types of the variable. In Java:

```java
// Note: this applies only to local variables, i.e. those declared within a method
var x = 5; // type inferred as int
```

In Rust:

```rust
let x = 5; // type inferred as i32
```

When expanding the first example to update the value of the variable
(reassignment), the behavior of Java and Rust differ:

```java
var x = 5;
x = 6;
System.out.println(x); // 6
```

In Rust, the identical statement will not compile:

```rust
let x = 5;
x = 6; // Error: cannot assign twice to immutable variable 'x'.
println!("{}", x);
```

In Rust, variables are _immutable_ by default. Once a value is bound to a name,
the variable's value cannot be changed. Variables can be made _mutable_ by
adding [`mut`][mut.rs] in front of the variable name:

```rust
let mut x = 5;
x = 6;
println!("{}", x); // 6
```

Rust offers an alternative to fix the example above that does not require
mutability through variable _shadowing_:

```rust
let x = 5;
let x = 6;
println!("{}", x); // 6
```

Java also supports shadowing, e.g. locals can shadow fields and type members can
shadow members from the base type. In Rust, the above example demonstrates
that shadowing also allows to change the type of a variable without changing
the name, which is useful if one wants to transform the data into different
types and shapes without having to come up with a distinct name each time.

## Constants

In Java, a constant is a `static final` field:

```java
static final double GOLDEN_RATIO = 1.618034;
```

The same in Rust looks like this:

```rust
const GOLDEN_RATIO: f64 = 1.618034;
```

See also:

- [Data races and race conditions] for more information around the implications
  of mutability
- [Scope and shadowing]
- [Memory management][memory-management-section] for explanations around
  _moving_ and _ownership_
- [Constants in Rust][constants]

[mut.rs]: https://doc.rust-lang.org/std/keyword.mut.html
[memory-management-section]: ../memory-management/index.md
[data races and race conditions]: https://doc.rust-lang.org/nomicon/races.html
[scope and shadowing]: https://doc.rust-lang.org/stable/rust-by-example/variable_bindings/scope.html#scope-and-shadowing
[constants]: https://doc.rust-lang.org/rust-by-example/custom_types/constants.html
