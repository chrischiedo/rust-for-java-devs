# Threads

The following table lists approximate mapping of threading types and methods in Java to Rust:

| Java                            | Rust                      |
| ------------------------------- | ------------------------- |
| `Thread`                        | `std::thread::Thread`     |
| `Thread.start`                  | `std::thread::spawn`      |
| `Thread.join`                   | `std::thread::JoinHandle` |
| `Thread.sleep`                  | `std::thread::sleep`      |
| `synchronized`                  | -                         |
| `ReentrantLock`                 | `std::sync::Mutex`        |
| `ReentrantReadWriteLock`        | `std::sync::RwLock`       |
| `Semaphore`                     | -                         |
| `Condition`                     | `std::sync::Condvar`      |
| `java.util.concurrent.atomic.*` | `std::sync::atomic`       |
| `volatile`                      | -                         |
| `ThreadLocal`                   | `std::thread_local`       |

Launching a thread and waiting for it to finish works the same way in Java
and Rust. Below is a simple Java program that creates a thread and then waits for it to end:

```java
Thread thread = new Thread(() -> {
                System.out.println("Hello from a thread!"); });

thread.start();
thread.join();  // wait for thread to finish
```

The same code in Rust looks as follows:

```rust
use std::thread;

fn main() {
    let thread = thread::spawn(|| println!("Hello from a thread!"));
    thread.join().unwrap(); // wait for thread to finish
}
```

Creating and initializing a thread object and starting a thread are two
different actions in Java whereas in Rust both happen at the same time with
`thread::spawn`.

Java 21 introduced the [Thread.Builder][builder] API for creating and starting both 
_platform_ and _virtual_ threads. Platform threads are the traditional threads in Java that are thin wrappers around OS threads - the thread that we created above is an example of a platform thread. [Virtual threads][virtual-threads] on the other hand are lightweight threads that are not directly tied to OS threads.

   [builder]: https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/Thread.Builder.html
   [virtual-threads]: https://openjdk.org/jeps/444

Here's how we can use the thread builder API to create a platform thread in Java:

```java
Thread platformThread = Thread.ofPlatform().unstarted(() -> {
                        System.out.println("Hello from a platform thread!"); });

platformThread.start();
platformThread.join();
```

And here's how we can create a virtual thread:

```java
Thread virtualThread = Thread.ofVirtual().unstarted(() -> {
                       System.out.println("Hello from a virtual thread!"); });

virtualThread.start();
virtualThread.join();
```

> Note: Rust does not have support for virtual threads.


## Working with thread pools

In Java, we can create a thread pool using the [Executor framework][ExecutorService].

   [ExecutorService]: https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/concurrent/ExecutorService.html

Consider the following Java example:

```java
static void concurrentProcessing() throws ExecutionException, InterruptedException {

    // Using Callable so a value can be returned after processing by a thread
    Callable<Integer> sportsNews =  () -> {
        for (int i = 0; i < 10; i++) {
            Thread.sleep(1000); // sleep for 1 second
            System.out.println("Manchester United is winning " + i);
        }
        // Just return some random number to illustrate return
        return 42;
    };

    Callable<Integer> leaguePoints =  () -> {
        for (int i = 0; i < 10; i++) {
            Thread.sleep(700); // sleep for 700 milliseconds
            int points = 40 + i;
            System.out.println("Manchester has " + points + " league points");
        }
        // Just return some random number
        return 24;
    };

    // A placeholder for Future objects
    List<Future<Integer>> futures = new ArrayList<>();

    // A temporary storage for results returned by threads
    List<Integer> results = new ArrayList<>();

    try (
        final ExecutorService service = Executors.newFixedThreadPool(2); // Create a pool of 2 threads
        ) {

        futures.add(service.submit(sportsNews));
        futures.add(service.submit(leaguePoints));

        for (var future : futures) {
            results.add(future.get());
        }
    }

    for (Integer result : results) {
        System.out.println("Got the result: " + result);
    }
}

public static void main(String[] args) {

    try {
        concurrentProcessing();
    } catch (InterruptedException | ExecutionException e) {
        e.printStackTrace();
    }

}
```

Here's the output of running the Java code above:

```
Manchester has 40 league points
Manchester United is winning 0
Manchester has 41 league points
Manchester United is winning 1
Manchester has 42 league points
Manchester has 43 league points
Manchester United is winning 2
Manchester has 44 league points
Manchester United is winning 3
Manchester has 45 league points
Manchester has 46 league points
Manchester United is winning 4
Manchester has 47 league points
Manchester United is winning 5
Manchester has 48 league points
Manchester United is winning 6
Manchester has 49 league points
Manchester United is winning 7
Manchester United is winning 8
Manchester United is winning 9
Got the result: 42
Got the result: 24
```

> Note: Your output might look slighly different.

Here's the Rust version of the code. We are using the [Rayon library][rayon] to create a thread pool.

```rust
use std::{thread, time};
use std::sync::Mutex;

fn sports_news() -> i32 {
    for i in 0..10 {
        let seconds = time::Duration::from_millis(1000);
        thread::sleep(seconds); // sleep for 1 second
        println!("Manchester United is winning {}", i);
    }
    
    // Just return some number to illustrate return
    return 42;
}

fn league_points() -> i32 {
    for i in 0..10 {
        let seconds = time::Duration::from_millis(700);
        thread::sleep(seconds); // sleep for 700 milliseconds
        let points = 40 + i;
        println!("Manchester has {} league points", points);
    }
    
    // Just return some number
    return 24;
}

fn concurrent_processing() {
    // A temporary storage for results returned by threads
    let result_vec = Mutex::new(Vec::new());

    let pool = rayon::ThreadPoolBuilder::new()
        .num_threads(2) // Create a pool of 2 threads
        .build()
        .unwrap();

    pool.install(|| {

        rayon::scope(|s| {
            
            let result_vec = &result_vec;

            // spawn thread to process sports news
            s.spawn(move |_| {
                let result1 = sports_news();
                
                result_vec.lock().unwrap().push(result1);
            });

            // spawn thread to process league points
            s.spawn(move |_| {
                let result2 = league_points();
                
                result_vec.lock().unwrap().push(result2);
            });
    
        });
    });
    
    for result in result_vec.lock().unwrap().iter() {
        println!("Got the result: {:?}", result);
    }
}

fn main() {
    
    concurrent_processing();
    
}
```

  [rayon]: https://docs.rs/rayon/latest/rayon/index.html


Here's the output of the Rust code above:

```
Manchester has 40 league points
Manchester United is winning 0
Manchester has 41 league points
Manchester United is winning 1
Manchester has 42 league points
Manchester has 43 league points
Manchester United is winning 2
Manchester has 44 league points
Manchester United is winning 3
Manchester has 45 league points
Manchester has 46 league points
Manchester United is winning 4
Manchester has 47 league points
Manchester United is winning 5
Manchester has 48 league points
Manchester United is winning 6
Manchester has 49 league points
Manchester United is winning 7
Manchester United is winning 8
Manchester United is winning 9
Got the result: 24
Got the result: 42
```
