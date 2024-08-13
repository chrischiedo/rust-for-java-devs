# Pattern Matching

Both Java and Rust provide facilities for pattern matching. We already saw some use of pattern matching in the [section for enums][enums].

Consider this Java method that makes use of an `if-then-else` statement:

```java
String fooBar(Integer number) {
    if (number % 2 == 0) {
        return "foo";
    } else if (number % 3 == 0) {
        return "bar";
    } else {
        throw new RuntimeException("some other number: " + number);
    }
}

System.out.println(fooBar(10)); // prints: foo
System.out.println(fooBar(15)); // prints: bar
System.out.println(fooBar(13)); // raises an exception and prints: some other number: 13
```

We can rewrite the code above to use pattern matching with `switch expression`:

```java
String fooBar(Integer number) {
    return switch (number) {
        case Integer n when n % 2 == 0 -> "foo";
        case Integer n when n % 3 == 0 -> "bar";
        default -> throw new RuntimeException("some other number: " + number);
    };
}

System.out.println(fooBar(10)); // prints: foo
System.out.println(fooBar(15)); // prints: bar
System.out.println(fooBar(13)); // raises an exception and prints: some other number: 13
```

Here's the Rust equivalent:

```rust
fn foo_bar(number: i32) -> String {
    match number {
        n if n % 2 == 0 => "foo".to_string(),
        n if n % 3 == 0 => "bar".to_string(),
                      _ => panic!("some other number: {}", number)
    }
}

fn main() {
    println!("{}", foo_bar(10)); // prints foo
    println!("{}", foo_bar(15)); // prints: bar
    println!("{}", foo_bar(13)); // panics and prints: some other number: 13
}
```

The `if` condition in each of the match arms is called a _match guard_ in Rust.

Let's consider the following Java interface:

```java
public interface Shape {

    public double area();
}
```

And the following types that implement the `Shape` interface (overriding the `area` method):

```java
record Circle(double radius) implements Shape {

    @Override
    public double area() {
        return Math.PI * radius * radius;
    }
}

record Rectangle(double length, double width) implements Shape {

    @Override
    public double area() {
        return length * width;
    }
}

record Triangle(double base, double height) implements Shape {

    @Override
    public double area() {
        return 0.5 * base * height;
    }
}
```

Now, here's how we can use pattern matching with switch expression to get the area of each type of shape:

```java
public static double getAreaOfShape(Shape shape) throws IllegalArgumentException {
    return switch (shape) {
    	case Circle c    -> c.area();
        case Rectangle r -> r.area();
        case Triangle t  -> t.area();
        default          -> throw new IllegalArgumentException("Unrecognized shape");
    };
}
```

And this is how we can exercise the method:

```java
Circle shape1 = new Circle(2.0);
double areaOfCircle = getAreaOfShape(shape1);
System.out.println("Area of Circle is: " + areaOfCircle); // prints: Area of Circle is: 12.566370614359172

Rectangle shape2 = new Rectangle(4.0, 2.0);
double areaOfRectangle = getAreaOfShape(shape2);
System.out.println("Area of Rectangle is: " + areaOfRectangle); // prints: Area of Rectangle is: 8.0

Triangle shape3 = new Triangle(4.0, 3.0);
double areaOfTriangle = getAreaOfShape(shape3);
System.out.println("Area of Triangle is: " + areaOfTriangle); // prints: Area of Triangle is: 6.0
```

Before the addition of pattern matching functionality in Java, this is how we would implement the `getAreaOfShape` method:

```java
public static double getAreaOfShape(Shape shape) throws IllegalArgumentException {
    if (shape instanceof Rectangle r) {
        return r.area();
    } else if (shape instanceof Circle c) {
        return c.area();
    } else if (shape instanceof Triangle t) {
        return t.area();
    } else {
        throw new IllegalArgumentException("Unrecognized shape");
    }
}
```

Here's the Rust equivalent code:

```rust
trait Shape {
    fn area(&self) -> f64;
}

struct Circle {
    radius: f64,
}

struct Rectangle {
    length: f64,
    width: f64,
}

struct Triangle {
    base: f64,
    height: f64,
}

impl Shape for Circle {
    fn area(&self) -> f64 {
        std::f64::consts::PI * self.radius * self.radius
    }
}

impl Shape for Rectangle {
    fn area(&self) -> f64 {
        self.length * self.width
    }
}

impl Shape for Triangle {
    fn area(&self) -> f64 {
        0.5 * self.base * self.height
    }
}

enum ShapeType {
    Circle(Circle),
    Rectangle(Rectangle),
    Triangle(Triangle),
}
```

We can then get the area using pattern matching as shown below:

```rust
fn get_area_of_shape(shape: ShapeType) -> f64 {
    
    match shape {
        ShapeType::Circle(circle) => circle.area(),
        ShapeType::Rectangle(rectangle) => rectangle.area(),
        ShapeType::Triangle(triangle) => triangle.area(),
    }
}
```

And here's how we can use the function:

```rust
fn main() {
    let shape1 = ShapeType::Circle(Circle { radius: 2.0 });
    let area_of_circle = get_area_of_shape(shape1);
    println!("The area of circle is {}", area_of_circle); // prints: The area of circle is 12.566370614359172
    
    let shape2 = ShapeType::Rectangle(Rectangle { length: 4.0, width: 2.0 });
    let area_of_rectangle = get_area_of_shape(shape2);
    println!("The area of rectangle is {}", area_of_rectangle); // prints: The area of rectangle is 8
    
    let shape3 = ShapeType::Triangle(Triangle { base: 4.0, height: 3.0 });
    let area_of_triangle = get_area_of_shape(shape3);
    println!("The area of triangle is {}", area_of_triangle); // prints: The area of triangle is 6
}
```

[enums]: ./custom-types/enums.md
