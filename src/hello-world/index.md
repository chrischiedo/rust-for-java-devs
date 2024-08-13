# Hello World

In keeping with the programming tradition, here's a simple `Hello World` program in Java:

```java
public class HelloWorld {

    public static void main(String[] args) {
        System.out.println("Hello World.");
    }
}
```

And here's the equivalent in Rust:

```rust
fn main() {
    println!("Hello World.");
}
```

A few things to note:

- There's much less ceremony in the Rust version of the program
- In Rust, a function is defined using the `fn` keyword
- In both cases, we need a `main` funtion/method as the entry point to our program
- Unlike in Java, Rust's `main` function isn't contained in a class
- Rust uses the `println!` _macro_ to print the string to the output (notice the bang `!`)

In Java's case, it's clear from the method signature that the main method returns nothing (hinted by the use of `void` as the return type).

Although it's not clear from the function signature, the Rust version also returns nothing. In the absense of an explicit return type, a Rust function returns void, which is represented by `()`.

This slightly modified Rust version works exactly the same way as the one above:

```rust
fn main() -> () {
    println!("Hello World.");
}
```