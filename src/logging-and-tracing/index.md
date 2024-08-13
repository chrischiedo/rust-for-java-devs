# Logging and Tracing

Java comes with a logging API, which is ideal for simple use-cases. Apart from the logging API, there are other logging frameworks for Java, like `Log4J` and 
`Logback`. The `Simple Logging Facade for Java (SLF4J)` provides an abstraction layer that allows you to easily switch logging frameworks if needed.

In Java, a minimal example for structured logging using the logging API could look like this:

```java
import java.util.logging.Logger;

public class ExampleLogger {
    
    private static final Logger LOGGER = Logger.getLogger(ExampleLogger.class.getName());

    public static void main(String[] args) {
        LOGGER.info("Printing Hello World");

        System.out.println("Hello World");
    }
}
```

In Rust, a lightweight logging facade is provided by [log][log.rs]. This can be used for relatively simple logging use-cases.

For something with more features like some of the Java logging frameworks, Tokio offers [`tracing`][tracing.rs]. `tracing` is a framework for instrumenting Rust applications to collect structured, event-based diagnostic information. [`tracing_subscriber`][tracing-subscriber.rs] can be used to implement and compose `tracing` subscribers. The same structured logging example from above with `tracing` and `tracing_subscriber` looks like this:

```rust
fn main() {
    // install global default ("console") collector.
    tracing_subscriber::fmt().init();
    tracing::info!("Printing Hello World");

    println!("Hello World");
}
```

[OpenTelemetry][opentelemetry.rs] offers a collection of tools, APIs, and SDKs
used to instrument, generate, collect, and export telemetry data based on the
OpenTelemetry specification. At the time of writing, the [OpenTelemetry Logging
API][opentelemetry-logging] is not yet stable and the Rust implementation [does
not yet support logging][opentelemetry-status.rs], but the tracing API is
supported.

[opentelemetry.rs]: https://crates.io/crates/opentelemetry
[tracing-subscriber.rs]: https://docs.rs/tracing-subscriber/latest/tracing_subscriber/
[opentelemetry-logging]: https://opentelemetry.io/docs/reference/specification/status/#logging
[opentelemetry-status.rs]: https://opentelemetry.io/docs/instrumentation/rust/#status-and-releases
[tracing.rs]: https://crates.io/crates/tracing
[log.rs]: https://crates.io/crates/log
