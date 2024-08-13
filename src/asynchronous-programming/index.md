# Asynchronous Programming

Unlike Java, Rust supports the asynchronous programming model. There are different 
_async runtimes_ for Rust, the most popular being [Tokio][tokio-rs]. The other options are [async-std] and [smol][smol-rs].

Here's a simple example of how to define an asynchronous function in Rust. The example relies on `async-std` for the implementation of `sleep`:

```rust
use std::time::Duration;
use async_std::task::sleep;

async fn format_delayed(message: &str) -> String {
    sleep(Duration::from_secs(1)).await;
    format!("Message: {}", message)
}
```

> Note: The Rust [`async`][async-ky] keyword transforms a block of code into a state
   machine that implements the [`Future`][future.rs] trait. This allows for writing asynchronous code sequentially.

See also:

- [Asynchronous programming in Rust]

[tokio-rs]: https://tokio.rs/
[async-std]: https://async.rs/
[smol-rs]: https://docs.rs/smol/latest/smol/
[async-ky]: https://doc.rust-lang.org/std/keyword.async.html
[future.rs]: https://doc.rust-lang.org/std/future/trait.Future.html
[Asynchronous programming in Rust]: https://rust-lang.github.io/async-book/
