# Smart Pointers

Rust (like C++) has smart pointer types. Unlike a normal pointer (a memory address that points to some other data), a smart pointer is a data structure that not only acts as a pointer, but also has additional capabilities (and metadata).

Different smart pointers are defined in the Rust standard library. Some of the most common are:

- `Box<T>` : for allocating values on the heap
- `Rc<T>`/ `Arc<T>` : reference counting type that enables multiple value ownership
- `RefCell<T>` : a type that enforces borrowing rules at runtime (and enables _interior mutability_)

We are going to briefly look at the `Box<T>` type in this section. For more about smart pointers in Rust, see the [Smart Pointers] chapter of the Rust book.

  [Smart Pointers]: https://doc.rust-lang.org/book/ch15-00-smart-pointers.html

## Using `Box<T>` to store data on the heap

Consider this record type in Java:

```java
record Point(double x, double y) {}

// create an instance of Point
Point p1 = new Point(10.5, 12.4);
Point p2 = new Point(10.5, 12.4);
Point p3 = new Point(14.2, 8.2);

System.out.println(p1); // prints: Point[x=10.5, y=12.4]

System.out.println(p1 == p2);      // false
System.out.println(p1.equals(p2)); // true
System.out.println(p1.equals(p3)); // false
```

The record class is a reference type in Java. Therefore, variable `p1` is a reference (pointer) to the object that is created using the `new` keyword. While the reference itself is stored on the stack, the object that it points to is allocated on the heap.

In Rust, structs are allocated on the stack by default. If you want to store the struct on the heap instead, then you'll have to `box` it (i.e. wrap it in a `Box<T>` smart pointer):

```rust
#![allow(dead_code)]

#[derive(Debug, PartialEq)]
struct Point {
    x: f64,
    y: f64,
}

fn main() {
    // create an instance of Point struct
    let p1 = Point { x: 10.5, y: 12.4 }; // lives on the stack

    let p2 = Point { x: 10.5, y: 12.4 };

    let p3 = Box::new(Point { x: 10.5, y: 12.4 }); // allocate on the heap

    let p4 = Box::new(p1); // exactly the same as the line above

    println!("{:?}", p1); // prints: Point { x: 10.5, y: 12.4 }

    assert_eq!(p1, p2);  // ok
    assert_eq!(p1, p3);  // fails
    assert_eq!(p1, *p3); // ok, use the dereference operator (*) to get to the value stored on the heap
    assert_eq!(p3, p4);  // ok
}
```

See also:

- [Smart Pointers] chapter of the Rust book.

[Smart Pointers]: https://doc.rust-lang.org/book/ch15-00-smart-pointers.html

