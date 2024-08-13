# Interfaces and Traits

Rust doesn't have interfaces like those found in Java. It has _traits_,
instead. Similar to an interface, a trait represents an abstraction and its
members form a contract that must be fulfilled when implemented on a type.

Here's a simple example of an `interface` in Java:

```java
interface Scalable {
	double scaleLength();
	double scaleWidth();
}
```

Here's the equivalent `trait` definition in Rust:

```rust
trait Scalable {
    fn scale_length(&self) -> f64;
    fn scale_width(&self) -> f64;
}
```

In Java, the `Rectangle` record (from the previous section on [structs]) can implement the `Scalable` interface as follows:

```java
record Rectangle(double length, double width) implements Scalable {
	
	// details from previous section omitted

	@Override
    public double scaleLength() {
        return length * 2;
    }

    @Override
    public double scaleWidth() {
        return width * 2;
    }
}
```

In Rust, the `Rectangle` struct (from the previous section on [structs]) can implement the
`Scalable` trait as follows:

```rust
impl Scalable for Rectangle {
    fn scale_length(&self) -> f64 {
        self.length * 2 as f64
    }
    
    fn scale_width(&self) -> f64 {
        self.width * 2 as f64
    }
}
```

Note that in the previous section on [structs], we implemented the `Display` trait for the Rectangle struct. Here's how the `Display` trait is declared:

```rust
pub trait Display {
    // Required method
    fn fmt(&self, f: &mut Formatter<'_>) -> Result<(), Error>;
}
```

Just the way interfaces can have default methods[^default] in Java (where a default
implementation body is provided as part of the interface definition), so can
traits in Rust. The type implementing the interface/trait can subsequently
provide a more suitable and/or optimized implementation if needed.

Also, just like in Java interfaces, traits in Rust can define static methods[^static] (and even constants). Technically, in Rust these are called _associated_ functions and constants.

## Extending Interfaces and Traits

In Java, an interface can extend another interface. Let's say we have an interface called `Shape` that's declared as follows:

```java
interface Shape {
	// default method
	default boolean isRectangular() {
		return true;
	}
}
```

The `Scalable` interface can then extend the `Shape` interface, like so:

```java
interface Scalable extends Shape {
	double scaleLength();
	double scaleWidth();
}
```

The `Rectangle` record that implements the `Scalable` interface now has access to the default method defined in the `Shape` interface as well.

```java
record Rectangle(double length, double width) implements Scalable {
	
	// you have access to the default method defined in the Shape interface.
}
```

Similar behaviour can be achieved in Rust using `supertraits` and `subtraits`. Here's the `Shape` trait:

```rust
trait Shape {
	// default method
    fn is_rectangular(&self) -> bool {
    	true
    }
}
```

The `Scalable` trait can then extend the `Shape` trait, like so:

```rust
trait Scalable: Shape {
    fn scale_length(&self) -> f64;
    fn scale_width(&self) -> f64;
}
```

In this case, `Shape` is the _supertrait_ while `Scalable` is the _subtrait_.

Now, any type that implements the `Scalable` trait must also implement the `Shape` trait as well (note that this is slightly different from the Java case with interfaces).

Here's the `Rectangle` struct from the previous section on [structs].

```rust
#![allow(dead_code)]

use std::fmt::*;

struct Rectangle {
    length: f64,
    width: f64,
}

impl Rectangle {
    // details from previous section omitted
}

impl Scalable for Rectangle {
    fn scale_length(&self) -> f64 {
        self.length * 2 as f64
    }
    
    fn scale_width(&self) -> f64 {
        self.width * 2 as f64
    }
}

impl Shape for Rectangle {}

```

We can now invoke the default method defined in the `Shape` trait as shown below:

```rust
fn main() {
    let rect = Rectangle::new(5.2, 4.8);

    println!("The Shape is a Rectangle: {}", rect.is_rectangular()); // Will print: The Shape is a Rectangle: true
}
```

## Marker Interfaces and Traits

Rust has marker traits, just like Java has marker interfaces. These are empty (no methods declared) traits/interfaces; their main purpose being to communicate certain type behaviour to the compiler.

`Cloneable` is an example of a marker interface in Java:

```java
public interface Cloneable { }
```

`Copy` is an example of a marker trait in Rust:

```rust
pub trait Copy: Clone { }
```

Notice how the `Copy` trait extends the `Clone` trait.

## Polymorphic Behaviour

Apart from class hierarchies, interfaces are a core means of achieving
_polymorphism_ via dynamic dispatch for cross-cutting abstractions. They enable
general-purpose code to be written against the abstractions represented by the
interfaces without much regard to the concrete types implementing them.

Since Rust doesn't have classes and consequently type hierarchies based on sub-classing, polymorphism can be achieved using [trait objects] (in a limited fashion). A trait
object is essentially a _v-table_ (virtual table) identified with the `dyn`
keyword followed by the trait name, as in `dyn Shape` (where `Shape` is the
trait name). Trait objects always live behind a pointer, either a reference
(e.g. `&dyn Shape`) or the heap-allocated `Box` (e.g. `Box<dyn Shape>`). This
is somewhat like in Java, where an interface is a reference type such that a
type cast to an interface is automatically boxed onto the managed heap.

One limitation of trait objects is that the original
implementing type cannot be recovered. In other words, whereas it's quite
common to downcast or test an interface to be an instance of some other
interface or sub- or concrete type, the same is not possible in Rust (without
additional effort and support).


[structs]: ./structs.md
[trait objects]: https://doc.rust-lang.org/book/ch17-02-trait-objects.html#using-trait-objects-that-allow-for-values-of-different-types

---
[^default]: Default methods in interfaces were introduced in Java 8.
[^static]: Static methods in interfaces were introduced in Java 8.
