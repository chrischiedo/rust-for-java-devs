# Lambdas and Closures

Java has a feature for defining anonymous functions in the form of 
_lambda expressions_[^lambda]. The syntax for a lambda expression in Java looks as follows:

```
 ( paramlist ) -> { /* method body */ }
```

In Rust, anonymous functions are called _closures_. The syntax for a closure expression in Rust looks as follows:

```
 | paramlist | { /* function body */ }
```

> Note: In both Java and Rust, the types of the parameters will typically be inferred.

In addition to closures, Rust has _function pointers_ that implement all three of the closure traits (`Fn`, `FnMut` and `FnOnce`). Rust makes a distinction between function pointers (where `fn`
defines a type) and closures: a closure can reference variables from its surrounding lexical scope, but not a function pointer.

While Java doesn't have function pointers, it provides a set of functional interfaces (in the `java.util.function` package) that can be used as target types for lambda expressions (and method references). You can also define your own functional interfaces as well (as we do in the Java code example below).

In Rust, functions and methods that accept closures are written with generic types that
are bound to one of the closure traits: `Fn`, `FnMut` and `FnOnce`. When it's time to provide a value for a function pointer or a closure, you would use a _closure expression_ (like `|x| x + 1`), which translates to something similar to a lambda expression in Java. Whether the closure expression creates a function pointer or a closure depends on whether the closure expression references its context or not.

When a closure captures variables from its environment then ownership rules come into play because the ownership ends up with the closure. For more information, see the “[Moving Captured Values Out of Closures and the Fn Traits][closure-move]” section of the Rust book.

  [closure-move]: https://doc.rust-lang.org/book/ch13-01-closures.html#moving-captured-values-out-of-closures-and-the-fn-traits

Let's look at a Rust example that showcases all these functionalities:

```rust
struct Customer {
    name: String,
    gross_income: f64,
}

impl Customer {
    pub fn new(name: String, gross_income: f64) -> Self {
        Self { name, gross_income }
    }
    
    pub fn name(&self) -> String {
        self.name.clone()
    }
    
    pub fn gross_income(&self)  -> f64 {
        self.gross_income
    }
}

// Note: the parameter func is of type fn, which is a function pointer
fn apply_tax_calc_function(func: fn(f64) -> f64, arg: f64) -> f64 {
    func(arg)
}

fn main() {
    let customer = Customer::new("Jane Doe".to_string(), 50000 as f64);

    let name = customer.name();
    let income = customer.gross_income();

    let tax_function_closure = |income| {
        let payable_tax: f64;
        if income < 30000 as f64 {
            payable_tax = income * 0.05;
        } else {
            payable_tax = income * 0.06;
        }
        payable_tax
    };

    let calculated_tax = apply_tax_calc_function(tax_function_closure, income); // we pass in a closure

    println!("The calculated tax for {} is {}", name, calculated_tax); // prints: The calculated tax for Jane Doe is 3000
}
```

Here's how we can achieve the equivalent functionality in Java:

```java
// we define our own functional interface
@FunctionalInterface
interface TaxFunction {
    double calculateTax(double grossIncome);
}

record Customer(String name, double grossIncome) {

    public double applyTaxCalcFunction(TaxFunction taxFunc) {
        double calculatedTax = taxFunc.calculateTax(grossIncome);
        return calculatedTax;
    }
}

public class TestClass {

    public static void main(String[] args) {

          // define a function as a lambda expression and assign the result to a variable
          TaxFunction taxFunction = (grossIncome) -> {
              double payableTax;

              if (grossIncome < 30000) {
                  payableTax = grossIncome * 0.05;
              } else {
                  payableTax = grossIncome * 0.06;
              }

              return payableTax;
          };

          Customer customer = new Customer("Jane Doe", 50000);

          String name = customer.name();

          double calculatedTax = customer.applyTaxCalcFunction(taxFunction); // we pass in a lambda expression

          System.out.println("The calculated tax for " + name + " is "+ calculatedTax); // prints: The calculated tax for Jane Doe is 3000.0
    }
}
```

## Higher-order functions

Higher-order functions are functions that accept other functions as arguments to allow for the caller to participate in the code of the called function. As we've already seen in the code examples above, in both Rust and Java, we can pass closures and lambda expressions to functions. But unlike in Java, Rust also allows _regular functions_ to be passed into other functions:

```rust
struct Customer {
    name: String,
    gross_income: f64,
}

impl Customer {
    pub fn new(name: String, gross_income: f64) -> Self {
        Self { name, gross_income }
    }
    
    pub fn name(&self) -> String {
        self.name.clone()
    }
    
    pub fn gross_income(&self)  -> f64 {
        self.gross_income
    }
}

// Note: the parameter func is of type fn, which is a function pointer
fn apply_tax_calc_function(func: fn(f64) -> f64, arg: f64) -> f64 {
    func(arg)
}

// define a regular function
fn tax_function_regular(income: f64) -> f64 {
    let payable_tax: f64;
    if income < 30000 as f64 {
        payable_tax = income * 0.05;
    } else {
        payable_tax = income * 0.06;
    }
    payable_tax
}

fn main() {
    let customer = Customer::new("Jane Doe".to_string(), 50000 as f64);

    let name = customer.name();
    let income = customer.gross_income();

    let calculated_tax = apply_tax_calc_function(tax_function_regular, income); // we pass in a regular function

    println!("The calculated tax for {} is {}", name, calculated_tax); // prints: The calculated tax for Jane Doe is 3000
}
```

---
[^lambda]: Lambda expressions were introduced in Java 8.
