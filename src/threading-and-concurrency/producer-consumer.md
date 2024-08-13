# Producer-Consumer Pattern

The producer-consumer pattern is a very common pattern for distributing work between threads where data is passed from producing threads to consuming threads without the need for sharing or locking. Java provides support for this pattern. The 
`java.util.concurrent` package provides the `BlockingQueue` interface that can be used to implement this pattern:

```java
public class ExampleClass {

    public static void main(String[] args) throws InterruptedException {

        BlockingQueue<String> messages = new LinkedBlockingQueue<>();

        AtomicBoolean isComplete = new AtomicBoolean(false);

        Thread producer = new Thread(() -> {
            try {
                for (int i = 1; i < 10; i++) {
                    messages.put("Message #" + i); // add a message to the queue
                }
            } catch (InterruptedException e) {
                e.printStackTrace();
            } finally {
                isComplete.set(true);
            }
        });

        producer.start();

        // Main thread is the consumer here
        while (!isComplete.get() || !messages.isEmpty()) {
            try {
                String message = messages.take(); // get a message from the queue
                System.out.println("The message received is: " + message);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }

        producer.join();
    }
}
```

The output of the code above looks as follows:

```
The message received is: Message #1
The message received is: Message #2
The message received is: Message #3
The message received is: Message #4
The message received is: Message #5
The message received is: Message #6
The message received is: Message #7
The message received is: Message #8
The message received is: Message #9
```

The same result can be achieved in Rust by using _channels_. The standard library primarily
provides `mpsc::channel`, which is a channel that supports multiple producers and a single consumer. A rough translation of the above Java example in Rust would look as follows:

```rust
use std::thread;
use std::sync::mpsc;
use std::time::Duration;

fn main() {
    let (tx, rx) = mpsc::channel(); // create a message channel

    let producer = thread::spawn(move || {
        for i in 1..10 {
            tx.send(format!("Message #{}", i)).unwrap(); // send a message to the channel
        }
    });

    // main thread is the consumer here
    for received in rx {
        println!("The message received is: {}", received);
    }

    producer.join().unwrap();
}
```

The output for the Rust code above is as follows:

```
The message received is: Message #1
The message received is: Message #2
The message received is: Message #3
The message received is: Message #4
The message received is: Message #5
The message received is: Message #6
The message received is: Message #7
The message received is: Message #8
The message received is: Message #9
```

