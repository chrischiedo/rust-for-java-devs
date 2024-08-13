# Generics

Generics in Java provide a way to create definitions for types and methods that
can be parameterized over other types. This improves code reuse, type-safety
and performance (e.g. avoid run-time casts).

Consider the following example of a generic `Point` type in Java:

```java
public record Point<T>(T x, T y) {}

// creating instances of Point
Point<Integer> p1 = new Point<>(10, 12);
Point<Double> p2 = new Point<>(10.5, 12.4);

```

Here's the Rust equivalent:

```rust
struct Point<T> {
    x: T,
    y: T,
}

fn main() {

    // creating instances of Point struct
    let p1 = Point { x: 10, y: 12, };
    let p2 = Point { x: 10.5, y: 12.4, };
}
```

Here's another example of a generic type in Java:

```java
record Rectangle<T>(T length, T width) {

    // Accessor method. Not necessary, added for illustration purposes
    public T length() {
        return length;
    }

    // Accessor method. Not necessary, added for illustration purposes
    public T width() {
        return width;
    }
}

Rectangle2<Integer> rect1 = new Rectangle2<>(10, 5);
Rectangle2<Double> rect2 = new Rectangle2<>(12.4, 6.2);

System.out.println("Length of rect1 is: " + rect1.length()); // prints: Length of rect1 is: 10
System.out.println("Length of rect2 is: " + rect2.length());// prints: Length of rect2 is: 12.4
```

Rust also has generics as shown by the equivalent of the above:

```rust
#![allow(dead_code)]


struct Rectangle<T> {
    length: T,
    width: T,
}

impl<T> Rectangle<T> {

    pub fn length(&self) -> &T {
        &self.length
    }

    pub fn width(&self)  -> &T {
        &self.width
    }
}


fn main() {
    let rect1 = Rectangle { length: 10, width: 5 };
    let rect2 = Rectangle { length: 12.4, width: 6.2 };

    println!("Length of rect1 is: {}", rect1.length()); // prints: Length of rect1 is: 10
    println!("Length of rect2 is: {}", rect2.length()); // prints: Length of rect2 is: 12.4
}
```

See also:

- [Generic data types]

[Generic data types]: https://doc.rust-lang.org/book/ch10-01-syntax.html

## Bounded type parameters 

In Java, bounded type parameters are used to specify generic types with restrictions related to inheritance hierarchies.

Consider the following example of a generic type that adds a timestamp to any value:

```java
import java.time.Instant;

record Timestamped<T extends Comparable<T>>(T value, Instant timestamp) 
    implements Comparable<Timestamped<T>> {

    public Timestamped(T value) {
        this(value, Instant.now());
    }

    @Override
    public int compareTo(Timestamped<T> o) {
        Timestamped<? extends Comparable<T>> that = o;
        return value.compareTo((T) that.value);
    }
}

Timestamped<String> timestamped1 = new Timestamped<>("Hello");
Timestamped<String> timestamped2 = new Timestamped<>("Hello", Instant.now());
Timestamped<String> timestamped3 = new Timestamped<>("Haha", Instant.now());
Timestamped<String> timestamped4 = new Timestamped<>( "House", Instant.now());

System.out.println(timestamped1.compareTo(timestamped2)); // prints: 0
System.out.println(timestamped1.compareTo(timestamped3)); // prints: 4 (+ve integer)
System.out.println(timestamped1.compareTo(timestamped4)); // prints: -10 (-ve integer)
```

The same result can be achieved in Rust as shown below:

```rust
#![allow(dead_code)]

use std::time::Instant;
use std::cmp::Ordering;

#[derive(Debug)]
struct Timestamped<T: Ord> { 
    value: T, 
    timestamp: Instant 
}

impl<T: Ord> Timestamped<T> {
    fn new(value: T) -> Self {
        Self { value, timestamp: Instant::now() }
    }
}

impl<T: Ord> Ord for Timestamped<T> {
    fn cmp(&self, other: &Self) -> Ordering {
        self.value.cmp(&other.value)
    }
}

impl<T> PartialOrd for Timestamped<T>
    where T: PartialEq + Ord {
    fn partial_cmp(&self, other: &Self) -> Option<Ordering> {
        Some(self.cmp(other))
    }
}

impl<T> PartialEq for Timestamped<T>
    where T: PartialEq + Ord {
    fn eq(&self, other: &Self) -> bool {
        self.value == other.value
    }
}

impl<T> Eq for Timestamped<T> where T: PartialEq + Ord {}

fn main() {
    
    let timestamped1 = Timestamped::new("Hello");
    let timestamped2 = Timestamped { value: "Hello", timestamp: Instant::now() };
    let timestamped3 = Timestamped { value: "Haha", timestamp: Instant::now() };
    let timestamped4 = Timestamped { value: "House", timestamp: Instant::now() };
    
    println!("{:?}", timestamped1.cmp(&timestamped2)); // prints: Equal
    println!("{:?}", timestamped1.cmp(&timestamped3)); // prints: Greater
    println!("{:?}", timestamped1.cmp(&timestamped4)); // prints: Less
}
```

Generic type constraints in Rust (also called [trait bounds][bounds.rs]), can be declared using the colon syntax (e.g. `T: Ord`) or the `where` clause (e.g. `where T: PartialEq + Ord`).

See also:

- [Traits as parameters]
- [Returning types that implement traits]

[bounds.rs]: https://doc.rust-lang.org/rust-by-example/generics/bounds.html
[Traits as parameters]: https://doc.rust-lang.org/book/ch10-02-traits.html#traits-as-parameters
[Returning types that implement traits]: https://doc.rust-lang.org/book/ch10-02-traits.html#returning-types-that-implement-traits


## Type erasure and monomorphization

During compilation, the Java compiler erases the parameterized data type in the byte code (replacing the parameterized type with its concrete equivalent). After compilation, the JVM does not see any distinction between the `parameterized` and the `raw` data types. This phenomenon is called `type erasure`.

The Rust compiler on the other hand performs `monomorphization` of the code that uses generics. Monomorphization is the process of turning generic code into specific code by filling in the concrete types that are used after compilation. This ensures that the resulting code is as performant as code written without using generic types.
