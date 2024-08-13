# Error Handling

In Java, an _exception_ is a type that inherits from the [`Throwable`][jvm-exception-class] class. This class is the superclass of all errors and exceptions in the Java language. Exceptions are thrown if a problem occurs in a code section. A thrown exception is passed up the stack until the application handles it or the program terminates.

Java distinguishes between _checked_ and _unchecked_ exceptions. The `Throwable` class has two immmediate descendant classes: `Error` and `Exception`. Subclasses of the `Error` class are fatal errors and are called _unchecked_ exceptions, and are not required to be caught. Subclasses of the `Exception` class (excluding `RuntimeException`) are called 
_checked_ exceptions and have to be handled in your code.

Rust does not have exceptions, but distinguishes between _recoverable_ and 
_unrecoverable_ errors instead. A recoverable error represents a problem that
should be reported, but for which the program continues. Results of operations
that can fail with recoverable errors are of type [`Result<T, E>`][rust-result],
where `E` is the type of the error variant. The [`panic!`][panic] macro stops
execution when the program encounters an unrecoverable error. An unrecoverable
error is always a symptom of a bug.

## Custom error types

In Java, you can create your own custom exceptions by extending the `Exception` class (or one of the classes from the `Throwable` hierarchy). Here's a simple example:

```java
public class EmployeeListNotFoundException extends Exception {

    public EmployeeListNotFoundException() { }

    public EmployeeListNotFoundException(String message) {
        super(message);
    }

    public EmployeeListNotFoundException(String message, Throwable cause) {
        super(message, cause);
    }
}
```

In Rust, one can implement the basic expectations for error values by
implementing the [`Error`][rust-std-error] trait. The minimal user-defined error
implementation in Rust is:

```rust
#[derive(Debug)]
pub struct EmployeeListNotFound;

impl std::fmt::Display for EmployeeListNotFound {
    fn fmt(&self, f: &mut std::fmt::Formatter<'_>) -> std::fmt::Result {
        f.write_str("Could not find employee list.")
    }
}

impl std::error::Error for EmployeeListNotFound {}
```

The equivalent to Java's `Throwable.getCause()` method is the `Error::source()` method in Rust. However, it is not required to provide an implementation for `Error::source()`, the blanket (default) implementation returns a `None`.

## Raising exceptions

To raise an exception in Java, throw an instance of the `Exception` class or any of the subclasses in its hierarchy:

```java
static void throwIfNegative(int value) {
    if (value < 0) {
        throw new IllegalArgumentException("Illegal argument value");
    }
}
```

For recoverable errors in Rust, return an `Ok` or `Err` variant from a method:

```rust
fn error_if_negative(value: i32) -> Result<(), &'static str> {
    if value < 0 {
        Err("Illegal argument value. (Parameter 'value')")
    } else {
        Ok(())
    }
}
```

The [`panic!`][panic] macro creates unrecoverable errors:

```rust
fn panic_if_negative(value: i32) {
    if value < 0 {
        panic!("Illegal argument value. (Parameter 'value')")
    }
}
```

## Error propagation

In Java, exceptions are passed up the stack until they are handled or the
program terminates.

In Rust, unrecoverable errors behave similarly, but handling
them is uncommon. Recoverable errors, however, need to be propagated and handled explicitly. Their presence is always indicated by the Rust function or method signature.

Catching an exception allows you to take action based on the presence or absence of an
error in Java:

```java
import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Paths;
import java.nio.file.StandardOpenOption;

static void write() {
    try {
        Files.write(
                Paths.get("file.txt"),
                "content to write".getBytes(),
                StandardOpenOption.CREATE,
                StandardOpenOption.TRUNCATE_EXISTING
        );
    } catch (IOException e) {
        System.out.println("Writing to file failed.");
    }
}
```

In Rust, this is roughly equivalent to:

```rust
fn write() {
    match std::fs::File::create("file.txt")
        .and_then(|mut file| std::io::Write::write_all(&mut file, b"content to write"))
    {
        Ok(_) => {}
        Err(_) => println!("Writing to file failed."),
    };
}
```

Frequently, recoverable errors need only be propagated instead of being handled.
For this, the method signature needs to be compatible with the types of the
propagated error. The [`?` operator][question-mark-operator] propagates errors
ergonomically:

```rust
fn write() -> Result<(), std::io::Error> {
    let mut file = std::fs::File::create("file.txt")?;
    std::io::Write::write_all(&mut file, b"content to write")?;
    Ok(())
}
```

**Note**: To propagate an error with the question mark operator, the error
implementations need to be _compatible_, as described in [_a shortcut for
propagating errors_][propagating-errors-rust-book]. The most general
"compatible" error type is the error [trait object] `Box<dyn Error>`.

## Stack traces

Throwing an unhandled exception in Java will cause the runtime to print a stack
trace that allows debugging the problem with additional context.

For unrecoverable errors in Rust, [`panic!` backtraces][panic-backtrace] offer a
similar behavior.

Recoverable errors in stable Rust do not yet support Backtraces, but it is
currently supported in experimental Rust when using the [provide method].

[jvm-exception-class]: https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/Throwable.html
[rust-result]: https://doc.rust-lang.org/std/result/enum.Result.html
[panic-backtrace]: https://doc.rust-lang.org/book/ch09-01-unrecoverable-errors-with-panic.html#using-a-panic-backtrace
[rust-std-error]: https://doc.rust-lang.org/std/error/trait.Error.html
[provide method]: https://doc.rust-lang.org/std/error/trait.Error.html#method.provide
[question-mark-operator]: https://doc.rust-lang.org/std/result/index.html#the-question-mark-operator-
[panic]: https://doc.rust-lang.org/std/macro.panic.html
[propagating-errors-rust-book]: https://doc.rust-lang.org/book/ch09-02-recoverable-errors-with-result.html#a-shortcut-for-propagating-errors-the--operator
[trait object]: https://doc.rust-lang.org/reference/types/trait-object.html
