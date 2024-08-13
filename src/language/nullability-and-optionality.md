# Nullability and Optionality

Java has the `Optional<T>`[^optional] utility class which represents a container object that may contain some value or null. In Java, `null` is often used to represent a value that is missing, absent or logically uninitialized. Here's an example of how we can use the Optional class:

```java
Optional<String> some = Optional.ofNullable("John");
Optional<String> none = Optional.ofNullable(null);

System.out.println(some.isPresent()); // true
System.out.println(some.get()); // prints John
System.out.println(none.isEmpty()); // true
```

Rust has no `null`. Optional or missing values are represented by the [`Option<T>`][option] type. The equivalent of the Java code above in Rust would be:

```rust
let some: Option<&str> = Some("John");
let none: Option<&str> = None;

assert_eq!(some.is_some(), true); // ok
println!("{}", some.unwrap());  // prints John
assert_eq!(none.is_none(), true); // ok
```

## Control flow with optionality

In Java, you may have used `if`/`else` statements to control the flow when using nullable values. For example:

```java
String name = some Name object that may be null...

if (name != null) {
    // do something with the name variable e.g. print it
    System.out.println(name);
} else {
    // deal with the null case or print a default name
    System.out.println("John");
}
```

We can rewrite the code above to use the Optional class as follows:

```java
String name = some Name object that may be null...

Optional<String> optionalName = Optional.ofNullable(name);

if (optionalName.isPresent()) {
    // do something with the name
    System.out.println(optionalName.get());
} else {
    // deal with the empty Optional or print a default name
    System.out.println("John");
}
```

In Rust, we can use pattern matching to achieve the same behavior:

```rust
let name: Option<&str> = Some("Arya");

match name {
    Some(name) => println!("{}", name),
    None => println!("John")  // if None, print default name instead
}
```

We can also make the Java code even more succinct:

```java
String name = some Name object that may be null...

String nameToPrint = Optional.ofNullable(name).orElse("John");

System.out.println(nameToPrint);
```

And the Rust code can be rewritten as below:

```rust
let name: Option<&str> = Some("Arya");

let name_to_print = name.unwrap_or("John"); // if name is None, use default value

println!("{}", name_to_print);
```

**Note**: If the default value is expensive to compute, you can use `unwrap_or_else` instead. It takes a closure as an argument, which allows you to lazily initialize the default value.

If we only care about the presence of a value (rather than its absence), then we can write code like this in Java:

```java
Optional<String> optionalName = Optional.of("Arya");

optionalName.ifPresent(name -> System.out.println("The name is " + name)); // prints: The name is Arya
```

The equivalent in Rust can be achieved using `if let`:

```rust
let name = Some("Arya");

if let Some(name) = name {
    println!("The name is {}", name); // prints: The name is Arya
}
```


[option]: https://doc.rust-lang.org/std/option/enum.Option.html
[optmap]: https://doc.rust-lang.org/std/option/enum.Option.html#method.map
[unwrap-or]: https://doc.rust-lang.org/std/option/enum.Option.html#method.unwrap_or

---
[^optional]: The `Optional` class was introduced in Java 8.
