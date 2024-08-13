# Ownership

Ownership is arguably one of Rust's most distinctive features. It allows Rust to make memory safety guarantees without needing a garbage collector.

In Rust, there can only be one owner of some memory, be that on the stack or heap, at any given time. Rust defines ownership rules that are enforced at compile time:

- Each value in Rust has a variable that's called it's _owner_.
- There can be _only one owner_ at a time.
- When the owner goes out of scope, the value will be _dropped_.

The Rust compiler assigns lifetimes and tracks ownership. It is possible to pass or yield ownership, which is called _moving_ in Rust.

See also:

- [Ownership] in Rust.

    [ownership]: https://doc.rust-lang.org/book/ch04-01-what-is-ownership.html

Consider the following Java code that works without any errors:

```java
record Point(int x, int y) {}

Point p1 = new Point(12, 10);
Point p2 = p1;

System.out.println(p1.x() + ", " + p1.y()); // prints: 12, 10
System.out.println(p2.x() + ", " + p2.y()); // prints: 12, 10
```

Now, let's look at the Rust version (fails with an error):

```rust
#![allow(dead_code, unused_variables)]

struct Point {
    x: i32,
    y: i32,
}

fn main() {
    let p1 = Point { x: 12, y: 10 }; // point owned by p1
    let p2 = p1;                     // ownership of point moved to p2 here

    println!("{}, {}", p1.x, p1.y);  // doesn't work, compiler error!
    println!("{}, {}", p2.x, p2.y);  // works fine, prints: 12, 10
}
```

The first statement in `main` will allocate `Point` and that memory will be
owned by `p1`. In the second statement, the ownership is moved from `p1` to `p2` and `p1` can no longer be used because it no longer owns anything or represents valid memory. The statement that tries to print the fields of the point via `p1` will fail compilation.

## Borrowing

One way of making the code compile is by letting `p2` borrow the value of `p1` instead of taking ownership, as shown below. The ampersand (`&`) indicates that `p2` takes a 
_reference_ to the value of `p1`.

```rust
fn main() {
    let p1 = Point { x: 12, y: 10 }; // point owned by p1
    let p2 = &p1;                    // p2 "borrows" point, doen't take ownership

    println!("{}, {}", p1.x, p1.y);  // works fine, prints: 12, 10
    println!("{}, {}", p2.x, p2.y);  // works fine, prints: 12, 10
}
```

## Cloning

Another alternative would be to _clone_ `p1`:

```rust
#[derive(Clone)] // this is required for cloning to work
struct Point {
    x: i32,
    y: i32,
}

fn main() {
    let p1 = Point { x: 12, y: 10 }; // point owned by p1
    
    let p2 = p1.clone(); // clone point instead of taking ownership

    println!("{}, {}", p1.x, p1.y);  // prints: 12, 10
    println!("{}, {}", p2.x, p2.y);  // prints: 12, 10
}
```

> Note that the `Point` struct needs to implement/derive the `Clone` trait in order for cloning to work.

## Variable Scope

Let's look at this Rust code again:

```rust
fn main() {
    let p1 = Point { x: 12, y: 10 }; // point owned by p1
    let p2 = p1;                     // p2 owns the point now

    println!("{}, {}", p2.x, p2.y);  // ok, uses p2
}   // point behind p2 is dropped
```

When `main` exits, `p1` and `p2` will go out of scope. The memory
behind `p2` will be released by virtue of the stack returning to its state
prior to `main` being called. In Rust, one says that the point behind `p2` was _dropped_. However, note that since `p1` yielded its ownership of the point to `p2`, there is nothing to drop when `p1` goes out of scope.

A `struct` in Rust can define code to execute when an instance is dropped by
implementing the [`Drop`][drop.rs] trait.

  [drop.rs]: https://doc.rust-lang.org/std/ops/trait.Drop.html

The rough equivalent of _dropping_ in Java would be an object _finalizer_: the `finalize()` method[^finalize] provided by the root `Object` class, that is called before the object is garbage collected. While a finalizer would be called _automatically_ by the GC at some future point, dropping in Rust is always instantaneous and deterministic; that is, it happens at the point the compiler has determined that an instance has no owner
based on scopes and lifetimes.

In Java, the equivalent of the `Drop` trait is the [`AutoCloseable`][AutoCloseable] interface, and is implemented by types to release any unmanaged resources or memory they hold. _Deterministic disposal_ is not enforced or guaranteed, but the `try-with-resources` statement in Java is typically used to scope an instance of an auto-closeable type such that it gets disposed deterministically, at the end of the `try-with-resources` statement's block.

  [AutoCloseable]: https://docs.oracle.com/javase/8/docs/api/java/lang/AutoCloseable.html

In Java, references are shared freely without much thought so the idea
of a single owner and yielding/moving ownership may seem very limiting in
Rust, but it is possible to have _shared ownership_ in Rust using the smart
pointer type [`Rc`][rc.rs]; it adds reference-counting. Each time [the smart pointer is cloned][Rc::clone], the reference count is incremented. When the
clone drops, the reference count is decremented. The actual instance behind
the smart pointer is dropped when the reference count reaches zero.

These points are illustrated by the following example that builds on the previous:

  [rc.rs]: https://doc.rust-lang.org/stable/std/rc/struct.Rc.html
  [Rc::clone]: https://doc.rust-lang.org/stable/std/rc/struct.Rc.html#method.clone

```rust
#![allow(dead_code, unused_variables)]

use std::rc::Rc;

struct Point {
    x: i32,
    y: i32,
}

impl Drop for Point {
    fn drop(&mut self) {
        println!("Point dropped!");
    }
}

fn main() {
    let p1 = Rc::new(Point { x: 12, y: 10 });
    let p2 = Rc::clone(&p1); // share with p2

    println!("p1 = {}, {}", p1.x, p1.y); // okay to use p1
    println!("p2 = {}, {}", p2.x, p2.y);
}

// prints:
// p1 = 12, 10
// p2 = 12, 10
// Point dropped!
```

Note that:

- `Point` implements the `drop` method of the `Drop` trait and prints a
  message when an instance of a `Point` is dropped.

- The point created in `main` is wrapped behind the smart pointer `Rc` and so
  the smart pointer _owns_ the point and not `p1`.

- `p2` gets a clone of the smart pointer that effectively increments the
  reference count to 2. Unlike the earlier example, where `p2` transferred its
  ownership of point to `p2`, both `p1` and `p2` own their own distinct clones of
  the smart pointer, so it is okay to continue to use `p1` and `p2`.

- The compiler will have determined that `p1` and `p2` go out of scope at the
  end of `main` and therefore injected calls to drop each. The `Drop`
  implementation of `Rc` will decrement the reference count and also drop what
  it owns if the reference count has reached zero. When that happens, the
  `Drop` implementation of `Point` will print the message, &ldquo;Point
  dropped!&rdquo; The fact that the message is printed once demonstrates that
  only one point was created, shared and dropped.

`Rc` is not thread-safe. For shared ownership in a multi-threaded program, the
Rust standard library offers [`Arc`][arc.rs] instead. The Rust language will
prevent the use of `Rc` across threads.

  [arc.rs]: https://doc.rust-lang.org/std/sync/struct.Arc.html

In Java, primitive types (like `int` and `double`) live on the stack and
reference types (like `class`, `interface`, and `record`) are heap-allocated. In Rust, the kind of type (basically `enum` or `struct`), does not determine where the backing memory will eventually live. By default, it is always on the stack, but just the way Java has the notion of autoboxing of primitive types, the way to allocate a type on the heap is to box it using [`Box`][box.rs]:

  [box.rs]: https://doc.rust-lang.org/std/boxed/struct.Box.html

```rust
let stack_point = Point { x: 12, y: 10 };
let heap_point = Box::new(Point { x: 12, y: 10 });
```

Like `Rc` and `Arc`, `Box` is a smart pointer, but unlike `Rc` and `Arc`, it
exclusively owns the instance behind it. All of these smart pointers allocate
an instance of their type argument `T` on the heap.

The `new` keyword in Java creates an instance of a type, and while members such
as `Box::new` and `Rc::new` that you see in the examples may seem to have a
similar purpose, `new` has no special designation in Rust. It's merely a
_conventional name_ that is meant to denote a factory. In fact they are called
_associated functions_ of the type, which is Rust's way of saying _static_
methods.

---
[^finalize]: The `finalization` mechanism has been deprecated since Java 9. In modern Java, the preferred approach for resource management is by the use of [cleaners], or [try-with-resources] statement.

  [cleaners]: https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/ref/Cleaner.html
  [try-with-resources]: https://dev.java/learn/catching-and-handling-exceptions/#anchor_6
