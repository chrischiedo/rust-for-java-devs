# Synchronization

When data is shared between threads, one needs to synchronize read-write
access to the data in order to avoid corruption. Java offers the `synchronized`
keyword as a synchronization primitive. When we use a synchronized block, Java internally uses a [monitor], also known as a _monitor lock_ to provide synchronization:

  [monitor]: https://en.wikipedia.org/wiki/Monitor_(synchronization)

```java
public class ExampleClass {

    static int data = 0;

    public static void main(String[] args) throws InterruptedException {

        var dataLock = new Object(); // declare a monitor lock object

        var threads = new ArrayList<Thread>();

        for (var i = 0; i < 10; i++) {
            var thread =  new Thread(() -> {
                for (var j = 0; j < 1000; j++) {
                    synchronized (dataLock) {
                        data++;
                    }
                }
            });

            threads.add(thread);
            thread.start();
        }

        for (var thread : threads) {
            thread.join();
        }

        System.out.println(data); // prints: 10000
    }
}
```

In Rust, one must make explicit use of concurrency structures like `Mutex`:

```rust
use std::thread;
use std::sync::{Arc, Mutex};

fn main() {
    let data = Arc::new(Mutex::new(0)); // (1)

    let mut threads = vec![];
    for _ in 0..10 {
        let data = Arc::clone(&data); // (2)
        let thread = thread::spawn(move || { // (3)
            for _ in 0..1000 {
                let mut data = data.lock().unwrap();
                *data += 1; // (4)
            }
        });
        threads.push(thread);
    }

    for thread in threads {
        thread.join().unwrap();
    }

    println!("{}", data.lock().unwrap()); // prints: 10000
}
```

A few things to note:

- Since the ownership of the `Mutex` instance and in turn the data it guards
  will be shared by multiple threads, it is wrapped in an `Arc` (1). `Arc`
  provides _atomic reference counting_, which increments each time it is cloned
  (2) and decrements each time it is dropped. When the count reaches zero, the
  mutex and in turn the data it guards are dropped.

- The closure instance for each thread receives ownership (3) of the _cloned
  reference_ (2).

- The `move` keyword (3) is _required_ to _move_ or pass the ownership of `data` (2) to 
  the closure for the thread.

- The pointer-like code that is `*data += 1` (4), is not some unsafe pointer
  access even if it looks like it. It's updating the data _wrapped_ in the
  [mutex guard].

Unlike the Java version, where one can render it _thread-unsafe_ by commenting out
the `synchronized` statement, the Rust version will refuse to compile if it's changed
in any way (e.g. by commenting out parts that renders it thread-unsafe). This
demonstrates that writing thread-safe code is the developer's responsibility
in Java, by careful use of synchronized structures, whereas in Rust, one
can rely on the compiler to enforce _thread-safety_.

The compiler is able to help because data structures in Rust are marked by
special traits: `Sync` and `Send`. [`Sync`][sync.rs] indicates that references to a type's instances are safe to share between threads. [`Send`][send.rs] indicates that it's safe to send instances of a type across thread boundaries. For more information, see the “[Fearless Concurrency]” chapter of the Rust book.

  [mutex guard]: https://doc.rust-lang.org/stable/std/sync/struct.MutexGuard.html
  [sync.rs]: https://doc.rust-lang.org/stable/std/marker/trait.Sync.html
  [send.rs]: https://doc.rust-lang.org/stable/std/marker/trait.Send.html
  [Fearless Concurrency]: https://doc.rust-lang.org/book/ch16-00-concurrency.html
