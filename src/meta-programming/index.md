# Meta Programming

Metaprogramming can be seen as a way of writing code that writes/generates other
code.

Java does not have facilities for "proper" metaprogramming. Instead, it offers capabilities for dynamic (runtime) introspection/reflection, mostly through the 
[Reflection API][reflection].

   [reflection]: https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/reflect/package-summary.html


Rust on the other hand has strong support for metaprogramming through a dedicated language feature: [macros]. There are two main types of Rust macros: `declarative macros` and `procedural macros`.

  [macros]: https://doc.rust-lang.org/book/ch19-06-macros.html

Declarative macros allow you to write control structures that take an
expression, compare the resulting value of the expression to patterns, and then
run the code associated with the matching pattern. Declarative macros are also sometimes referred to as _macros by example_.

An example of a declarative macro is the `println!` macro that we've used quite a bit. It is used for printing some text to the standard output: `println!("Some text")`. Here's the definition for the macro:

```rust
macro_rules! println {
    () => {
        $crate::print!("\n")
    };
    ($($arg:tt)*) => {{
        $crate::io::_print($crate::format_args_nl!($($arg)*));
    }};
}
```

To learn more about writing declarative macros, refer to the Rust reference
chapter [macros by example] or [The Little Book of Rust Macros].

[Procedural macros] are different from declarative macros. Those accept some code
as an input, operate on that code, and produce some code as an output.

  [procedural macros]: https://doc.rust-lang.org/reference/procedural-macros.html
  [macros by example]: https://doc.rust-lang.org/reference/macros-by-example.html
  [The Little Book of Rust Macros]: https://veykril.github.io/tlborm/

> Note: Rust does not support reflection.

## Function-like macros

Function-like macros take the following form: `function!(...)`

The following code snippet defines a function-like macro named
`print_something`, which is generating a `print_it` method for printing the
"Something" string.

In the `lib.rs` file:

```rust
extern crate proc_macro;
use proc_macro::TokenStream;

#[proc_macro]
pub fn print_something(_item: TokenStream) -> TokenStream {
    "fn print_it() { println!(\"Something\") }".parse().unwrap()
}
```

In the `main.rs` file:

```rust
use replace_crate_name_here::print_something;
print_something!();

fn main() {
    print_it();
}
```

## Derive macros

Derive macros can create new items given the token stream of a struct, enum, or
union. An example of a derive macro is the `#[derive(Clone)]`, which is generates the needed code for making the input struct/enum/union implement the `Clone` trait.

In order to understand how to define a custom derive macro, read the rust reference for [derive macros].

[derive macros]: https://doc.rust-lang.org/reference/procedural-macros.html#derive-macros

## Attribute macros

Attribute macros define new attributes which can be attached to Rust items.
While working with asynchronous code, if making use of Tokio, the first step
will be to decorate the new asynchronous main with an attribute macro like in the
following example:

```rust
#[tokio::main]
async fn main() {
    println!("Hello world");
}
```

In order to understand how to define a custom derive macro, read the rust reference for [attribute macros].

[attribute macros]: https://doc.rust-lang.org/reference/procedural-macros.html#attribute-macros

