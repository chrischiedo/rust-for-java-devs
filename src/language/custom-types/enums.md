# Enumerated types (`enum`)

In Java, an `enum` is a specialized type of class that has limited functionality. An enum holds a small number of possible permissible values of a type.

Here's an example of an enum in Java:

```java
enum PrimaryColor {
    RED,
    GREEN,
    BLUE
}
```

Rust has an identical syntax for declaring the same enum:

```rust
enum PrimaryColor {
    Red,
    Green,
    Blue
}
```

Being specialized classes in Java, enums can have member fields and methods. Here's an example for illustration purposes:

```java
enum RainbowColor {

    RED(1),
    ORANGE(2),
    YELLOW(3),
    GREEN(4),
    BLUE(5),
    INDIGO(6),
    VIOLET(7); // the semi colon at the end of list required for enums with parameters

    private final int number;
    private final String name;

    public int getNumber() {
        return number;
    }

    public String getName() {
        return name;
    }

    RainbowColor(int number) {
        this.number = number;
        this.name = switch (number) {
            case 1 -> "RED";
            case 2 -> "ORANGE";
            case 3 -> "YELLOW";
            case 4 -> "GREEN";
            case 5 -> "BLUE";
            case 6 -> "INDIGO";
            case 7 -> "VIOLET";
            default -> throw new RuntimeException("Illegal: " + number);
        };
    }
}
```

Here's how we could exercise the `RainbowColor` enum:

```java
public class RainbowColorTest {

    public static void main(String[] args) {
        RainbowColor color = RainbowColor.BLUE;

        String name = color.getName();

        System.out.println(name); // prints: BLUE
    }
}
```

A slightly similar (not a 1:1 mapping) version of the `RainbowColor` enum in Rust is shown below:

```rust
#[derive(Debug)] // enables formatting in "{:?}"
enum RainbowColor {
    Red = 1,
    Orange = 2,
    Yellow = 3,
    Green = 4,
    Blue = 5,
    Indigo = 6,
    Violet = 7,
}

impl RainbowColor {
    fn new(number: i32) -> Result<RainbowColor, Box<dyn std::error::Error>> {
        use RainbowColor::*;
        match number {
            1 => Ok(Red),
            2 => Ok(Orange),
            3 => Ok(Yellow),
            4 => Ok(Green),
            5 => Ok(Blue),
            6 => Ok(Indigo),
            7 => Ok(Violet),
            _ => return Err(format!("Illegal: {}", number).into())
        }
    }
}
```

The `new()` function returns a `RainbowColor` in a `Result` indicating success
(`Ok`) if `number` is valid. Otherwise it panics:

```rust
let color = RainbowColor::new(5);
println!("{color:?}"); // prints: Ok(Blue)

let color = RainbowColor::new(10);
println!("{color:?}"); // prints: Err("Illegal: 10")
```

An `enum` type in Rust can also serve as a way to design (discriminated) union
types, which allow different _variants_ to hold data specific to each variant.
For example:

```rust
enum IpAddr {
    V4(u8, u8, u8, u8),
    V6(String),
}

let home = IpAddr::V4(127, 0, 0, 1);
let loopback = IpAddr::V6(String::from("::1"));
```

Enums in Rust are most similar to [_algebraic data types_][adt] in functional languages, such as OCaml and Haskell.

[adt]: https://en.wikipedia.org/wiki/Algebraic_data_type
