# References and Lifetimes

Consider this Rust code:

```rust
#![allow(dead_code, unused_variables)]

#[derive(Debug, PartialEq)]
struct Point {
    x: i32,
    y: i32,
}

fn main() {
    let p1 = Point { x: 12, y: 10 };
    let p2 = &p1; // p2 takes a reference to the value of p1

    assert_eq!(Point { x: 12, y: 10 }, p1);  // ok
    assert_eq!(Point { x: 12, y: 10 }, p2);  // fails
    assert_eq!(Point { x: 12, y: 10 }, *p2); // ok, we use the dereference operator (*) to get the value
}
```

In Rust, references allow you to _refer_ to some value without taking ownership of it. References are indicated by the use of an ampersand (`&`). In order to get to the value that a reference points to, we use the dereference operator (`*`).


Consider the following `Rectangle` struct and its implementation block:

```rust
#[derive(Debug)]
struct Rectangle {
    length: i32,
    width: i32,
}

impl Rectangle {
    pub fn new(length: i32, width: i32) -> Self {
        Self { length, width }
    }

    pub fn length(&self) -> i32 {
        self.length
    }

    pub fn width(&self)  -> i32 {
        self.width
    }
}
```

Here's a function to calculate the area of a rectangle. Notice that we are passing in the `Rectangle` by value:

```rust
fn calculate_area(rect: Rectangle) -> i32 {
    rect.length() * rect.width()
}
```

When we exercise the function as shown below, we get an error. Because we are passing 
_by value_, ownership of rect is _moved into_ the `calculate_area()` function:

```rust
fn main() {
    let rect = Rectangle::new(6, 4);
    
    
    let area_of_rect = calculate_area(rect); // value moved into function
    
    
    println!("{}", area_of_rect); // prints: 24
    println!("{:?}", rect);       // won't work, rect no longer available here
}
```

One (inefficient) solution would be to make `Rectangle` _cloneable_:

```rust
#[derive(Debug, Clone)]
struct Rectangle {
    length: i32,
    width: i32,
}

fn main() {
    let rect = Rectangle::new(6, 4);
    
    let area_of_rect = calculate_area(rect.clone());

    println!("{}", area_of_rect); // prints: 24
    println!("{:?}", rect);       // works fine, prints: Rectangle { length: 6, width: 4 }
}
```

When we are dealing with a lot of objects, then cloning wouldn't be a great solution.

A better approach would be to make the `calculate_area()` function take a _reference_ to the `Rectangle` (i.e. `&Rectangle`) as input. In other words, pass the value _by reference_.

```rust
fn calculate_area(rect: &Rectangle) -> i32 {
    rect.length() * rect.width()
}
```

And now we can use the function as follows:

```rust
#[derive(Debug)] // no more Clone
struct Rectangle {
    length: i32,
    width: i32,
}

fn main() {
    let rect = Rectangle::new(6, 4);
    
    
    let area_of_rect = calculate_area(&rect); // takes a reference as input
    
    
    println!("{}", area_of_rect); // prints: 24
    println!("{:?}", rect);       // works fine, prints: Rectangle { length: 6, width: 4 }
}
```


Finally, let's consider the following case that doesn't compile:

```rust
fn main() {
    let rect1;
    
    {
        let rect2 = Rectangle::new(6, 4);
        rect1 = &rect2;
    } // rect2 goes out of scope/is dropped here
    
    // rect1, which holds a reference to rect2 is invalid here
    println!("{:?}", rect1); // won't work: borrowed value (&rect2) does not live long enough
}
```

Here's the same code with _lifetime_ annotations:

```rust
fn main() {
    let rect1;                            // -----------+--- 'a
                                          //            |
    {                                     //            |  
        let rect2 = Rectangle::new(6, 4); // --+-- 'b   |
        rect1 = &rect2;                   //   |        |
    }                                     // --+        |
                                          //            |
    println!("{:?}", rect1);              //            |
}                                         // -----------+
```

As you can see, the lifetime of rect2 (`'b`) is shorter than that of rect1 (`'a`). That's what the error message indicates: `borrowed value (&rect2) does not live long enough`. Therefore trying to access rect2 after it's been dropped results in a compile-time error.

> The lifetime annotation `'a` is pronounced "_tick A_".

The Rust compiler has a _borrow checker_ that keeps track of variable lifetimes to determine whether all borrows are valid.
 
The version below works fine:

```rust
fn main() {
    let rect2 = Rectangle::new(6, 4); // -----------+--- 'b
                                      //            |
    let rect1 = &rect2;               // --+-- 'a   |  
                                      //   |        |
    println!("{:?}", rect1);          //   |        |
                                      //---+        |
}                                     // -----------+
```

See also:

- [Lifetimes] in Rust.

[Lifetimes]: https://doc.rust-lang.org/rust-by-example/scope/lifetime.html
