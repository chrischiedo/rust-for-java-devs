# Control Flow

Like Java, Rust has control flow constructs like `if` expressions, `while` and `for` loops. 

Consider the following code snippet in Java:

```java
int number = 50;

if (number % 3 == 0) {
    System.out.println("fizz");
} else if (number % 5 == 0) {
    System.out.println("buzz");
} else {
    System.out.println("nothing special...");
}
```

This is the equivalent in Rust:

```rust
let number = 50;

if number % 3 == 0 {
    println!("fizz");
} else if number % 5 == 0 {
    println!("buzz");
} else {
    println!("nothing special...");
}
```

Notice that in Rust we don't have parenthesis around the condition, like in Java.

Consider the following `while` loop in Java:

```java
int number = 10;

while (number > 0) {
    System.out.println(number);
    number--; // or number = number - 1; or number -= 1;
}
```

This is the equivalent in Rust:

```rust
let mut number = 10;

while number > 0 {
    println!("{}", number);
    number -= 1; // or number = number - 1;
}
```

Note that `number--` doesn't work in Rust. Also, `number` is declared as `mut`.

## Looping through a collection with `for`

Java supports two types of `for` loops:

- The traditional C-style `for` loop
- The enhanced `for` loop (also known as `for-each` loop)

Consider the following Java example that uses C-style `for` loop:

```java
int[] numbers = { 10, 20, 30, 40, 50 };

for (int i = 0; i < numbers.length; i++) {
    System.out.println(numbers[i]);
}
```

Rust does not support C-style `for` loops. But we can get the same results using the 
`range` syntax:

```rust
let numbers = [10, 20, 30, 40, 50];
    
for i in 0..numbers.len() {
    println!("{}", numbers[i]);
}
```

Here's how we can use the enhanced `for` loop in Java:

```java
int[] numbers = { 10, 20, 30, 40, 50 };

for (int number : numbers) {
    System.out.println(number);
}
```

Here's the equivalent in Rust:

```rust
let numbers = [10, 20, 30, 40, 50];
    
for number in numbers {
    println!("{}", number);
}
```

## Defining infinite loops

There are a few ways of defining infinite loops in Java. We'll consider two:

1. Using `while`:

```java
while (true) {
    // do something
}
```
2. Using `for`:

```java
for (;;) {
    // do something
}
```

Here's how you would define an infinite loop in Rust:

```rust
loop {
    // do something
}
```

Both Java and Rust support `break` and `continue` statements that can be used to break out of loops.

## The ternary operator in Java

Consider the following trivial method in Java:

```java
String getResult(int score) {
    return score >= 70 ? "pass" : "fail"; // using ternary operator
}

System.out.println(getResult(60)); // prints: fail
System.out.println(getResult(80)); // prints: pass
```

An equivalent version in Rust would look like this:

```rust
fn get_result(score: i32) -> String {
	let result = if score >= 70 { "pass" } else { "fail" }; // result has type &str
	return result.to_string();
}

fn main() {
	println!("{}", get_result(60)); // prints: fail
	println!("{}", get_result(80)); // prints: pass
}
```

An alternative way of writing the Rust function is shown below:

```rust
fn get_result(score: i32) -> String {
	if score >= 70 { "pass".to_string() } else { "fail".to_string() }
}
```
