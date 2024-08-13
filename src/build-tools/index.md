# Build Tools

The two popular build tools in the Java ecosystem are Maven and Gradle. Rust has [Cargo] (`cargo`), which is both a build tool and a package manager.

As an example, to compile/build a project using Maven you would use `mvn compile`. The equivalent in Rust using cargo would be `cargo build`.

[cargo]: https://doc.rust-lang.org/cargo/

## Building

To build an executable for a Java project, you can use `mvn package`, which will compile the project sources into a single executable `jar` file. The jar file can typically be run on any platform that has JVM installed. [`cargo build`][cargo-build] in Rust does the same, except the Rust compiler statically links (although there exist other [linking options][linkage]) all code into a single, platform-dependent, binary.

In Java, you can use a tool like [GraalVM][graalvm] to create a native binary executable, similar to what Rust does with `cargo build`.

See also:

- [Crate]

[graalvm]: https://www.graalvm.org/
[cargo-build]: https://doc.rust-lang.org/cargo/commands/cargo-build.html#cargo-build1
[linkage]: https://doc.rust-lang.org/reference/linkage.html
[crate]: https://doc.rust-lang.org/book/ch07-01-packages-and-crates.html

## Dependencies

In Java, the contents of the `pom.xml` file define the build options and
project dependencies. In Rust, when using Cargo, a `Cargo.toml` declares the
dependencies for a package.

A typical `pom.xml` file would look like:

```xml
<project>

  <groupId>com.example</groupId>
  <artifactId>simplerestapi</artifactId>
  <version>0.0.1-SNAPSHOT</version>
  <name>rest-api-demo</name>
  <description>Simple REST API demo project</description>

  <properties>
    <java.version>21</java.version>
  </properties>

  <dependencies>

    <dependency>
      <groupId>org.postgresql</groupId>
      <artifactId>postgresql</artifactId>
      <version>42.7.3</version>
    </dependency>
    ...

  </dependencies>


  <build>
    ....
  </build>

</project>
```

The equivalent `Cargo.toml` in Rust is defined as:

```toml
[package]
name = "rest-api-demo"
version = "0.1.0"
description = "Simple REST API demo project"

[dependencies]
tokio = "1.0.0"
```

Cargo follows a convention that `src/main.rs` is the crate root of a binary
crate with the same name as the package. Likewise, Cargo knows that if the
package directory contains `src/lib.rs`, the package contains a library crate
with the same name as the package.

## Package registries

The most common package registry for Java is the [Maven Central][maven] repository, whereas Rust packages are usually shared via [crates.io].

[maven]: https://central.sonatype.com/
[crates.io]: https://crates.io
