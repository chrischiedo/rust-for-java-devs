# Equality

When comparing for equality in Java, this may refer to testing for _object equality_ in
some cases, and in other cases it may refer to testing for _reference equality_, which tests whether two variables refer/point to the same underlying object in memory. Every custom type can be compared for equality because it inherits from the `Object` root class (which provides an `equals()` method).

For example, when comparing for object and reference equality in Java:

```java
record Point(int x, int y) {}

var a = new Point(1, 2);
var b = new Point(1, 2);
var c = new Point(2, 2);
var d = a;

System.out.println(a == b); // (1) false
System.out.println(a.equals(b)); // (2) true
System.out.println(a.equals(c)); // (2) false
System.out.println(a == d); // (1) true
System.out.println(a.equals(d)); // true
```

1. The equality operator `==` is used to compare for reference equality. In this case,
   variables `a` and `b` are referring/pointing to different objects in memory.

2. The `equals()` method is used to compare for object equality, i.e. whether two 
   distinct objects have the same content.


Equivalently in Rust:

```rust
#[derive(Copy, Clone)]
struct Point(i32, i32);

fn main() {
    let a = Point(1, 2);
    let b = Point(1, 2);
    let c = Point(2, 2);
    let d = a;
    println!("{}", a == b); // Error: "an implementation of `PartialEq<_>` might be missing for `Point`"
    println!("{}", a.eq(&b));
    println!("{}", a.eq(&c));
}
```

The compiler error above illustrates that in Rust equality comparisons are
_always_ related to a trait implementation. To support a comparison using `==`,
a type must implement [`PartialEq`][partialeq.rs].

Fixing the example above means deriving `PartialEq` for `Point`. Per default,
deriving `PartialEq` will compare all fields for equality, which therefore have
to implement `PartialEq` themselves.

```rust
#[derive(Copy, Clone, PartialEq)]
struct Point(i32, i32);

fn main() {
    let a = Point(1, 2);
    let b = Point(1, 2);
    let c = Point(2, 2);
    let d = a;

    println!("{}", a == b);   // true
    println!("{}", a.eq(&b)); // true
    println!("{}", a.eq(&c)); // false
    println!("{}", a.eq(&d)); // true
}
```

See also:

- [`Eq`][eq.rs] for a stricter version of `PartialEq`.

[partialeq.rs]: https://doc.rust-lang.org/std/cmp/trait.PartialEq.html
[eq.rs]: https://doc.rust-lang.org/std/cmp/trait.Eq.html
