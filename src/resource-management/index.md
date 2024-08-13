# Resource Management

The previous section on [memory management] explains the differences between Java
and Rust when it comes to GC, ownership and finalizers. It is highly recommended that you read it.

This section is limited to providing an example of a fictional
_database connection_ involving a SQL connection to be properly
closed/disposed/dropped.

Here's the Java version:

```java
import java.sql.Connection;

public class DatabaseConnection implements AutoCloseable {

    final String connectionString;
    Connection connection; // the Connection type implements AutoCloseable

    public DatabaseConnection(String connectionString) {
        this.connectionString = connectionString;
    }

    @Override
    public void close() throws Exception {
        // making sure to close the Connection
        this.connection.close();
        System.out.println("Closing connection: " + this.connectionString);
    }
}

// try-with-resources statement
try (
        var db1 = new DatabaseConnection("server=A; database=db1");
        var db2 = new DatabaseConnection("server=A; database=db2")
        ) {
    // ...code using "db1" and "db2"...
} // "close()" of "db1" and "db2" called here; when their scope ends
```

And here's the Rust equivalent:

```rust
struct DatabaseConnection(&'static str);

impl DatabaseConnection {
    // ...functions for using the database connection...
}

impl Drop for DatabaseConnection {
    fn drop(&mut self) {
        // ...closing connection...
        self.close_connection();
        // ...printing a message...
        println!("Closing connection: {}", self.0)
    }
}

fn main() {
    let _db1 = DatabaseConnection("Server=A;Database=DB1");
    let _db2 = DatabaseConnection("Server=A;Database=DB2");
    // ...code for making use of the database connection...
} // "Dispose" of "db1" and "db2" called here; when their scope ends
```

[memory management]: ../memory-management/index.md
