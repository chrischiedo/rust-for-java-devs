# Environment and Configuration

## Accessing environment variables

Java provides access to environment variables via the `System.getenv` method. This method retrieves the value of an environment variable at runtime.

```java
final String name = "EXAMPLE_ENVIRONMENT_VARIABLE";

String value = System.getenv(name);

if (value == null || value.isEmpty()) {
    System.out.println("Environment variable '" + name + "' not set.");
} else {
    System.out.println("Environment variable '" + name + "' set to '" + value + "'.");
}
```

Rust provides the same functionality of accessing an environment variable at
runtime via the `var` and `var_os` functions from the `std::env` module.

`var` function returns a `Result<String, VarError>`, either returning the
variable if set or returning an error if the variable is not set or it is not
valid Unicode.

`var_os` has a different signature giving back an `Option<OsString>`, either
returning some value if the variable is set, or returning None if the variable
is not set. An `OsString` is not required to be valid Unicode.

```rust
use std::env;


fn main() {
    let key = "ExampleEnvironmentVariable";
    match env::var(key) {
        Ok(val) => println!("{key}: {val:?}"),
        Err(e) => println!("couldn't interpret {key}: {e}"),
    }
}
```

```rust
use std::env;

fn main() {
    let key = "ExampleEnvironmentVariable";
    match env::var_os(key) {
        Some(val) => println!("{key}: {val:?}"),
        None => println!("{key} not defined in the enviroment"),
    }
}
```

Rust also provides the functionality for accessing an environment variable at
compile time. The `env!` macro from `std::env` expands the value of the variable
at compile time, returning a `&'static str`. If the variable is not set, an
error is emitted.

```rust
use std::env;

fn main() {
    let example_env_variable = env!("ExampleEnvironmentVariable");
    println!("{example_env_variable}");
}
```

In Java, compile time access to environment variables can be achieved, but in a
less straightforward way, using [Reflection API][reflection]. Generally this is not recommended in modern Java.

[reflection]: https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/reflect/package-summary.html

## Configuration

Java does not have built-in support for configuration management. In order to work with configuration, we need to use third-party libraries, such as the Apache Commons Configuration library.

Here's the Maven dependency for the Apache Commons Configuration library that we can add to our `pom.xml` file:

```xml
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-configuration2</artifactId>
    <version>2.11.0</version>
</dependency>
```

And here's how we can make use of the library:

```java
import org.apache.commons.configuration2.Configuration;
import org.apache.commons.configuration2.builder.fluent.Configurations;
import org.apache.commons.configuration2.ex.ConfigurationException;

public class ExampleClass {

    public static void main(String[] args) {

        Configurations configs = new Configurations();

        try {
            // Load environment variables
            Configuration configuration = configs.systemEnvironment();

            // Retrieve the value of the environment variable "ExampleEnvVariable"
            String exampleEnvVariable = configuration.getString("ExampleEnvVariable");

            System.out.println(exampleEnvVariable);
        } catch (ConfigurationException e) {
            e.printStackTrace();
        }
    }
}
```

A similar configuration experience in Rust is available via use of third-party
crates such as [figment] or [config].

See the following example making use of the [config] crate:

```rust
use config::{Config, Environment};

fn main() {
    let builder = Config::builder().add_source(Environment::default());

    match builder.build() {
        Ok(config) => {
            match config.get_string("example_env_variable") {
                Ok(v) => println!("{v}"),
                Err(e) => println!("{e}")
            }
        },
        Err(_) => {
            // something went wrong
        }
    }
}

```

[figment]: https://crates.io/crates/figment
[config]: https://crates.io/crates/config
