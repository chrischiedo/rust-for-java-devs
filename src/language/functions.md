# Functions

Unlike in Java where methods are mainly used to add behaviour to types (and therefore are tied to those types), Rust supports stand-alone functions. Rust _also_ supports methods (and _associated_ functions) that are tied to types (`structs` and `enums`).

Consider the following example method in Java:

```java
// A method to calculate the area of a rectangle
double areaOfRectangle(double length, double width) {
    return length * width;
}
```

This is how the equivalent function looks like in Rust:

```rust
// A function to calculate the area of a rectangle
fn area_of_rectangle(length: f64, width: f64) -> f64 {
	length * width // No semi-colon needed here. The expression is evaluated and the result returned
}
```

This would still work but it's not _idiomatic_ Rust:

```rust
fn area_of_rectangle(length: f64, width: f64) -> f64 {
	return length * width; // using a return statement works, but it's not idiomatic Rust
}
```

Here are a few things to note:

- In Rust, functions are defined with `fn`.
- Rust uses `snake_case` for function names (and for variable names as well).
- Unlike in Java, types are declared second in Rust (`length: f64`).
- In Rust, `->` signifies the return type of a function.
- In Rust, the _idiomatic way_ to return from a function is to _not_ terminate with 
  semi-colon.
