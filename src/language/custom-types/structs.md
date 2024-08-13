# Structures (`struct`)

Structures in Rust, defined with the `struct` keyword, resemble `struct` types in C/C++. In Java, the struct type can be approximated using a `record` class (when used as a "_data carrier_"). Here's a high-level comparison between Rust structs and Java record classes:

| Rust structs                                | Java records                            |
| ------------------------------------------  | --------------------------------------- |
| They are allocated on the stack by default. | Being reference types, they are allocated on the heap by default. |
| A struct can implement multiple traits.     | A record can implement multiple interfaces. |
| Structs cannot be sub-classed.              | Records cannot be sub-classed/extended. |
| Methods for a struct are defined separately in an _implementation block_ (`impl`). | Just like normal classes in Java, a record class can have methods. |


In Java, a `record class` is a way to model an _immutable data_ carrier. In
Rust, a `struct` is the primary construct for modeling any data structure (the
other being an `enum`). This means that we can also use a normal Java class (with some modifications) to represent a Rust struct.

Here's a simple example of a `struct` in Rust:

```rust
struct Point {
    x: f64,
    y: f64,
}

fn main() {
    // creating an instance of Point struct
    let p = Point {
        x: 10.5, 
        y: 12.4,
    };

    println!("Value of x is: {}", p.x);
    println!("Value of y is: {}", p.y);
}
```

Here's the equivalent in Java:

```java
record Point(double x, double y) {}

// creating an instance of Point
Point p = new Point(10.5, 12.4);

System.out.println("Value of x is: " + p.x());
System.out.println("Value of y is: " + p.y());
```

A `record` class (or a normal `class`) in Java has object equality and copy semantics by default. And so you are able to do things like this out of the box: 

```java
Point p1 = new Point(10.5, 12.4);
Point p2 = new Point(10.5, 12.4);
Point p3 = p1;
Point p4 = new Point(8.5, 14.8);

System.out.println(p1 == p2);      // false
System.out.println(p1.equals(p2)); // true
System.out.println(p1 == p3);      // true
System.out.println(p3.equals(p4)); // false
```

In Rust on the other hand, you need to annotate the struct with the
[`#derive` attribute][derive] and list the traits to be implemented:

  [derive]: https://doc.rust-lang.org/stable/reference/attributes/derive.html

```rust
#[derive(Copy,      // enables copy-by-value semantics
         Clone,     // required by Copy
         PartialEq, // enables value equality (==)
         Eq,        // stricter version of PartialEq
         Hash       // enables hash-ability for use in map types
)]
struct Point {
    x: f64,
    y: f64,
}
```

Consider the following `record` representing a rectangle in Java:

```java
record Rectangle(double length, double width) {

    // Constructor. Please see Note 1.
    public Rectangle(double length, double width) {
        this.length = length;
        this.width = width;
    }

    // Accessor method. Please see Note 2.
    public double length() {
        return length;
    }

    // Accessor method. Please see Note 2.
    public double width() {
        return width;
    }

    // Static method
    public static double area(double length, double width) {
         return length * width;
    }

    @Override
    public String toString() {
        return "Rectangle with length: " + length + " and width: " + width + " has been created.";
    }
}
```

Notes:

1. This is strictly not necessary. Added for the sake of comparison with the
   Rust version.

2. Having the accessor methods is strictly not necessary. Added for the sake of comparison with the Rust version.


The equivalent in Rust would be:

```rust
#![allow(dead_code)]

use std::fmt::*;

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

impl Display for Rectangle {
    fn fmt(&self, f: &mut Formatter<'_>) -> Result {
        write!(f, "Rectangle with length: {}, and width: {} has been created.", self.length, self.width)
    }
}
```

Note that a `record` in Java inherits the `toString()` method from the `Record` class (which extends `Object`) and
therefore it _overrides_ the base implementation to provide a custom string
representation. Since there is no inheritance in Rust, the way a type
advertises support for some _formatted_ representation is by implementing the
`Display` trait. This then enables an instance of the struct to
participate in formatting, such as shown in the call to `println!` below:

```rust
fn main() {
    let rect = Rectangle::new(5.2, 4.8);
    println!("{rect}"); // Will print: Rectangle with length: 5.2, and width: 4.8 has been created.
}
```
