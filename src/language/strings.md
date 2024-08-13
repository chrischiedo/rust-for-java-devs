# Strings

There are two string types in Rust: `String` and `&str`. The former is
allocated on the heap and the latter is a slice of a `String` or a `&str`.

The mapping of those to Java is shown in the following table:

| Rust               | Java                 | Note        |
| ------------------ | -------------------- | ----------- |
| `&str`             | `String`             | see Note 1. |
| `Box<str>`         | `String`             | see Note 2. |
| `String`           | `String`             |             |
| `String` (mutable) | `StringBuilder`      | see Note 2. |

There are differences in working with strings in Rust and Java, but the
equivalents above should be a good starting point. One of the differences is
that Rust strings are UTF-8 encoded, but JVM strings are UTF-16 encoded.
Further JVM strings are immutable, but Rust strings can be mutable when declared
as such, for example `let s = &mut String::from("hello");`.

There are also differences in using strings due to the concept of ownership. To
read more about ownership with the String Type, see the [Rust book][ownership-string-type-example].

[ownership-string-type-example]: https://doc.rust-lang.org/book/ch04-01-what-is-ownership.html#the-string-type

Notes:

1. In Rust, `&str` (pronounced: _string slice_) is an immutable string reference type.

2. The `Box<str>` type in Rust is equivalent to the `String` type in JVM. The
   difference between the `Box<str>` and `String` types in Rust is that the
   former stores pointer and size while the latter stores pointer, size, and
   capacity, allowing `String` to grow in size. This is similar to the
   `StringBuilder` type in JVM once the Rust `String` is declared mutable.

Java:

```java
String str = "Hello, World!";

StringBuilder sb1 = new StringBuilder("Hello, World!");

StringBuilder sb2 = new StringBuilder();
sb2.append("Hello");
```

Rust:

```rust
let str1: &str = "Hello, World!";
let str2 = Box::new("Hello World!");

let mut sb1 = String::from("Hello World!");

let mut sb2 = String::new();
sb2.push_str("Hello");
```

## String Literals

String literals in Java are immutable `String` types and allocated on the heap.
In Rust, they are `&'static str`, which is immutable and has a global lifetime
and does not get allocated on the heap; they're embedded in the compiled binary.

Java

```java
String str = "Hello, World!";
```

Rust

```rust
let str: &'static str = "Hello, World!";
```

Unlike Java, Rust can represent verbatim string literals as raw string literals.

Rust

```rust
let str = r#"Hello, \World/!"#;
```


## String Interpolation

Java lacks native support for String interpolation[^interpolation] in comparison to languages like [C#][String-interpolation.net]. The most common way of implementing string interpolation in Java is by using the `format()` method from the `String` class. Here's an example:

```java
String name = "John";
int age = 42;
String result = String.format("Person { Name: %s, Age: %d }", name, age);

// Alternative using the '+' operator
String result2 = "Person { Name: " + name + ", Age: " + age + " }";
```

Like Java, Rust does not have a built-in string interpolation feature. Instead, the
`format!` macro is used to format a string. The following example shows how to
use string interpolation in Rust:

```rust
let name = "John";
let age = 42;
let result = format!("Person {{ name: {name}, age: {age} }}");
```

Custom types can also be interpolated in Java due to the fact that
the `toString()` method is available for each type as it's inherited from `Object`.

```java
public class Person {
    private String name;
    private int age;

    // getters and setters omitted

    @Override
    public String toString() {
        return String.format("Person { Name: %s, Age: %d }", name, age);
    }
}

// Calling from main method
Person person = new Person();
person.setName("John");
person.setAge(42);
System.out.println(person);
```

In Rust, there is no default formatting implemented/inherited for each type.
Instead, the `std::fmt::Display` trait must be implemented for each type that
needs to be converted to a string.

```rust
use std::fmt::*;

struct Person {
    name: String,
    age: i32,
}

impl Display for Person {
    fn fmt(&self, f: &mut Formatter<'_>) -> Result {
        write!(f, "Person {{ name: {}, age: {} }}", self.name, self.age)
    }
}

let person = Person {
    name: "John".to_owned(),
    age: 42,
};

println!("{person}");
```

Another option is to use the `std::fmt::Debug` trait. The `Debug` trait is
implemented for all standard types and can be used to print the internal
representation of a type. The following example shows how to use the `derive`
attribute to print the internal representation of a custom struct using the
`Debug` macro. This declaration is used to automatically implement the `Debug`
trait for the `Person` struct:

```rust
#[derive(Debug)]
struct Person {
    name: String,
    age: i32,
}

let person = Person {
    name: "John".to_owned(),
    age: 42,
};

println!("{person:?}");
```

> Note: Using the :? format specifier will use the `Debug` trait to print the
> struct, where leaving it out will use the `Display` trait.

See also:

- [Rust by Example - Debug](https://doc.rust-lang.org/stable/rust-by-example/hello/print/print_debug.html?highlight=derive#debug)


[String-interpolation.net]: https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/tokens/interpolated

---
[^interpolation]: Java now has [String Templates] as a preview feature in Java 21 and Java 22.

  [String Templates]: https://openjdk.org/jeps/430
