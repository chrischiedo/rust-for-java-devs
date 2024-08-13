# Memory Management

Like Java (and other languages with automatic memory management), Rust has _memory-safety_ to avoid a whole class of bugs
related to memory access, and which end up being the source of many security
vulnerabilities in software. However, Rust can guarantee memory-safety at
_compile-time_; there is no run-time (like the JVM) making checks. The one
exception here is array bound checks that are done by the compiled code at
_run-time_. But unlike Java, it is also [possible to write unsafe code in Rust][unsafe-rust], and the language has the `unsafe` keyword to mark
functions and blocks of code where memory-safety is no longer guaranteed.

  [unsafe-rust]: https://doc.rust-lang.org/book/ch19-01-unsafe-rust.html

Rust has no garbage collector (GC). All memory management is entirely the
responsibility of the developer. That said, _safe Rust_ has rules around
ownership that ensure memory is freed _as soon as_ it's no longer in use (e.g.
when leaving the scope of a block or a function). The compiler does a
tremendous job, through (compile-time) static analysis, of helping manage that
memory through _ownership rules_. If violated, the compiler rejects the code
with a compilation error.

In the JVM, there is no concept of ownership of memory beyond the GC roots
(static fields, local variables on a thread's stack, CPU registers, handles,
etc.). It is the GC that walks from the roots during a collection to detemine
all memory in use by following references and purging the rest. When designing
types and writing code, a Java developer can remain oblivious to ownership,
memory management and even how the garbage collector works for the most part,
except when performance-sensitive code requires paying attention to the amount
and rate at which objects are being allocated on the heap. In contrast, Rust's
ownership rules require the developer to explicitly think and express
ownership at all times and it impacts everything from the design of functions,
types, data structures to how the code is written. On top of that, Rust has
strict rules about how data is used such that it can identify at compile-time,
data [race conditions] as well as corruption issues (requiring thread-safety)
that could potentially occur at run-time.

  [race conditions]: https://doc.rust-lang.org/nomicon/races.html

This section focuses on the following important concepts related to memory management in Rust:

- [Ownership](ownership.md)
- [References and Lifetimes](references-and-lifetimes.md)
