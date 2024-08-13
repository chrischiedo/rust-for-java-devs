# Members

## Constructors

Rust does not have any notion of constructors. Instead, you just write factory
functions that return an instance of the type. The factory functions can be
stand-alone or _associated functions_ of the type. In Java terms, associated
functions are like having static methods on a type. Conventionally, if there
is just one factory function for a `struct`, it's named `new`:

```rust
struct Rectangle {
    length: f64,
    width: f64,
}

impl Rectangle {
    pub fn new(length: f64, width: f64) -> Self {
        Self { length, width }
    }
}
```

Since Rust functions (associated or otherwise) do not support overloading; the
factory functions have to be named uniquely. For example, below are some
examples of so-called constructors or factory functions available on `String`:

- `String::new`: creates an empty string.
- `String::with_capacity`: creates a string with an initial buffer capacity.
- `String::from_utf8`: creates a string from bytes of UTF-8 encoded text.
- `String::from_utf16`: creates a string from bytes of UTF-16 encoded text.

In the case of an `enum` type in Rust, the variants act as the constructors.
See [the section on enumeration types][enums] for more.

See also:

- [Constructors are static, inherent methods (C-CTOR)][rs-api-C-CTOR]

  [enums]: enums.md
  [rs-api-C-CTOR]: https://rust-lang.github.io/api-guidelines/predictability.html?highlight=new#constructors-are-static-inherent-methods-c-ctor

## Methods (static & instance-based)

Like Java, Rust types (both `enum` and `struct`), can have static and
instance-based methods. In Rust-speak, a _method_ is always instance-based and
is identified by the fact that its first parameter is named `self`. The `self`
parameter has no type annotation since it's always the type to which the
method belongs. A static method is called an _associated function_. In the
example below, `new` is an associated function and the rest (`length`, `width`
and `area`) are methods of the type:

```rust
struct Rectangle {
    length: f64,
    width: f64,
}

impl Rectangle {
    pub fn new(length: f64, width: f64) -> Self {
        Self { length, width }
    }

    pub fn length(&self) -> f64 {
        self.length
    }

    pub fn width(&self)  -> f64 {
        self.width
    }

    pub fn area(&self)  -> f64 {
        self.length() * self.width()
    }
}
```

## Properties

In Java, it is generally good practice for fields of a type (e.g. a class) to be private. They are then
protected/encapsulated by property members with accessor methods (`getters` and
`setters`) to read or write to those fields. The accessor methods can contain extra
logic, for example, to either validate the value when being set or compute a
value when being read. Rust only has methods [where a getter is named after the
field (in Rust method names can share the same identifier as a field) and the
setter uses a `set_` prefix][get-set-name.rs].

  [get-set-name.rs]: https://github.com/rust-lang/rfcs/blob/master/text/0344-conventions-galore.md#gettersetter-apis

Below is an example showing how property-like accessor methods typically look
for a type in Rust:

```rust
struct Rectangle {
    length: f64,
    width: f64,
}

impl Rectangle {
    pub fn new(length: f64, width: f64) -> Self {
        Self { length, width }
    }

    // like property getters (each shares the same name as the field)

    pub fn length(&self) -> f64 { self.length }
    pub fn width(&self)  -> f64 { self.width }

    // like property setters

    pub fn set_length(&mut self, val: f64) { self.length = val }
    pub fn set_width(&mut self, val: f64) { self.width = val }

    // like computed properties

    pub fn area(&self)  -> i32 {
        self.length() * self.width()
    }
}
```

## Visibility/Access modifiers

Java has a number of accessibility or visibility modifiers:

- `private`
- `protected`
- `package private`
- `public`

In Rust, a compilation is built-up of a tree of modules where modules contain
and define [_items_][items] like types, traits, enums, constants and
functions. Almost everything is private by default. One exception is, for
example, _associated items_ in a public trait, which are public by default.
This is similar to how members of a Java interface declared without any public
modifiers in the source code are public by default. Rust only has the `pub`
modifier to change the visibility with respect to the module tree. There
are variations of `pub` that change the scope of the public visibility:

- `pub(self)`
- `pub(super)`
- `pub(crate)`
- `pub(in PATH)`

For more details, see the [Visibility and Privacy][privis] section of The Rust
Reference.

  [privis]: https://doc.rust-lang.org/reference/visibility-and-privacy.html
  [items]: https://doc.rust-lang.org/reference/items.html

The table below is an approximation of the mapping of Java and Rust modifiers:

| Java                          | Rust         | Note        |
| ----------------------------- | ------------ | ----------- |
| `private`                     | (default)    | See note 1. |
| `protected`                   | N/A          | See note 2. |
| `package private`             | `pub(crate)` |             |
| `public`                      | `pub`        |             |

1. There is no keyword to denote private visibility; it's the default in Rust.

2. Since there are no class-based type hierarchies in Rust, there is no
   equivalent of `protected`.

## Mutability

When designing a type in Java, it is the responsiblity of the developer to
decide whether the type is mutable or immutable. Java does support an immutable design
for "_data carriers_" with `record classes`.

In Rust, mutability is expressed on methods through the type
of the `self` parameter as shown in the example below:

```rust
struct Point {
    x: f64,
    y: f64,
}

impl Point {
    pub fn new(x: f64, y: f64) -> Self {
        Self { x, y }
    }

    // self is not mutable

    pub fn x(&self) -> f64 { self.x }
    pub fn y(&self) -> f64 { self.y }

    // self is mutable

    pub fn set_x(&mut self, val: f64) { self.x = val }
    pub fn set_y(&mut self, val: f64) { self.y = val }
}
```
