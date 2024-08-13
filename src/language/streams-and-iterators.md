# Streams and Iterators

This section discusses Java's [Stream API][stream] within the context and for the purpose of transforming sequences (`Iterator<E>`/`Iterable<T>`) and typically collections like lists, sets and maps.

## `Iterable<T> Interface`

The equivalent of Java's `Iterable<T>` in Rust is [`IntoIterator`][into-iter.rs].
Just as an implementation of `Iterable<T>.iterator()` returns a
`Iterator<T>` in Java, an implementation of `IntoIterator::into_iter`
returns an [`Iterator`][iter.rs]. However, when it's time to iterate over the
items of a container advertising iteration support through the said types,
both languages offer syntactic sugar in the form of looping constructs for
iteratables. In Java, there is the enhanced `for` statement (also known as the `for-each` loop).

```java
int[] values = { 1, 2, 3, 4, 5 };

StringBuilder output = new StringBuilder();

for (var value : values) {
    if (output.length() > 0) {
        output.append(", ");
    }
    output.append(value);
}

System.out.println(output); // prints: 1, 2, 3, 4, 5
```

In Rust, the equivalent is simply `for`:

```rust
use std::fmt::Write;

fn main() {
    let values = [1, 2, 3, 4, 5];
    let mut output = String::new();

    for value in values {
        if output.len() > 0 {
            output.push_str(", ");
        }
        // ! discard/ignore any write error
        _ = write!(output, "{value}");
    }

    println!("{output}");  // Prints: 1, 2, 3, 4, 5
}
```

The `for` loop over an iterable essentially gets desugared to the following:

```rust
use std::fmt::Write;

fn main() {
    let values = [1, 2, 3, 4, 5];
    let mut output = String::new();

    let mut iter = values.into_iter();      // get iterator
    while let Some(value) = iter.next() {   // loop as long as there are more items
        if output.len() > 0 {
            output.push_str(", ");
        }
        _ = write!(output, "{value}");
    }

    println!("{output}");
}
```

Here's a Java example that uses the `forEach()` method:

```java
Map<String, String> houses = new HashMap<>(Map.of(
                "Stark", "Winter Is Coming",
                "Targaryen", "Fire and Blood",
                "Lannister", "Hear Me Roar",
                "Arryn", "As High as Honor",
                "Tully", "Family, Duty, Honor",
                "Greyjoy", "We Do Not Sow",
                "Baratheon", "Ours is the Fury",
                "Tyrell", "Growing Strong",
                "Martell", "Unbowed, Unbent, Unbroken",
                "Hightower", "We Light the Way"
                ));

houses.entrySet()
        .stream()
        .forEach(house -> System.out.println(
                          "House: " + house.getKey() 
                                    + "," + " Motto: "
                                    + house.getValue()));

```

The output looks as follows:

```
House: Lannister, Motto: Hear Me Roar
House: Targaryen, Motto: Fire and Blood
House: Baratheon, Motto: Ours is the Fury
House: Hightower, Motto: We Light the Way
House: Martell, Motto: Unbowed, Unbent, Unbroken
House: Tyrell, Motto: Growing Strong
House: Tully, Motto: Family, Duty, Honor
House: Stark, Motto: Winter Is Coming
House: Arryn, Motto: As High as Honor
House: Greyjoy, Motto: We Do Not Sow
```

Here's the Rust equivalent:

```rust
use std::collections::HashMap;

fn main() {

    let houses = HashMap::from([
            ("Stark".to_owned(), "Winter Is Coming".to_owned()),
            ("Targaryen".to_owned(), "Fire and Blood".to_owned()),
            ("Lannister".to_owned(), "Hear Me Roar".to_owned()),
            ("Arryn".to_owned(), "As High as Honor".to_owned()),
            ("Tully".to_owned(), "Family, Duty, Honor".to_owned()),
            ("Greyjoy".to_owned(), "We Do Not Sow".to_owned()),
            ("Baratheon".to_owned(), "Ours is the Fury".to_owned()),
            ("Tyrell".to_owned(), "Growing Strong".to_owned()),
            ("Martell".to_owned(), "Unbowed, Unbent, Unbroken".to_owned()),
            ("Hightower".to_owned(), "We Light the Way".to_owned())
    ]);


    for (key, value) in houses.iter() {
        println!("House: {}, Motto: {}", key, value);
    }
}
```

The output for the Rust version looks as follows:

```
House: Baratheon, Motto: Ours is the Fury
House: Stark, Motto: Winter Is Coming
House: Targaryen, Motto: Fire and Blood
House: Martell, Motto: Unbowed, Unbent, Unbroken
House: Lannister, Motto: Hear Me Roar
House: Tyrell, Motto: Growing Strong
House: Arryn, Motto: As High as Honor
House: Tully, Motto: Family, Duty, Honor
House: Hightower, Motto: We Light the Way
House: Greyjoy, Motto: We Do Not Sow
```

Rust's ownership and data race condition rules apply to all instances and
data, and iteration is no exception. So while looping over an array might look
straightforward and very similar to Java, one has to be mindful about ownership
when needing to iterate the same collection/iterable more than once. The
following example iterates the list of integers twice, once to print their sum
and another time to determine and print the maximum integer:

```rust
fn main() {
    let values = vec![1, 2, 3, 4, 5];

    // sum all values
    let mut sum = 0;
    for value in values {
        sum += value;
    }
    println!("sum = {sum}");

    // determine maximum value
    let mut max = None;
    for value in values {
        if let Some(some_max) = max { // if max is defined
            if value > some_max {     // and value is greater
                max = Some(value)     // then note that new max
            }
        } else {                      // max is undefined when iteration starts
            max = Some(value)         // so set it to the first value
        }
    }
    println!("max = {max:?}");
}
```

However, the code above is rejected by the compiler due to a subtle
difference: `values` has been changed from an array to a [`Vec<int>`][vec.rs],
a _vector_, which is Rust's type for growable arrays (like `List<E>` in Java).
The first iteration of `values` ends up _consuming_ each value as the integers
are summed up. In other words, the ownership of _each item_ in the vector
passes to the iteration variable of the loop: `value`. Since `value` goes out
of scope at the end of each iteration of the loop, the instance it owns is
dropped. Had `values` been a vector of heap-allocated data, the heap memory
backing each item would get freed as the loop moved to the next item. To fix
the problem, one has to request iteration over _shared_ references via
`&values` in the `for` loop. As a result, `value` ends up being a shared
reference to an item as opposed to taking its ownership.

  [vec.rs]: https://doc.rust-lang.org/stable/std/vec/struct.Vec.html

Below is the updated version of the previous example that compiles. The fix is
to simply replace `values` with `&values` in each of the `for` loops.

```rust
fn main() {
    let values = vec![1, 2, 3, 4, 5];

    // sum all values
    let mut sum = 0;
    for value in &values {
        sum += value;
    }
    println!("sum = {sum}");

    // determine maximum value
    let mut max = None;
    for value in &values {
        if let Some(some_max) = max { // if max is defined
            if value > some_max {     // and value is greater
                max = Some(value)     // then note that new max
            }
        } else {                      // max is undefined when iteration starts
            max = Some(value)         // so set it to the first value
        }
    }
    println!("max = {max:?}");
}
```

The ownership and dropping can be seen in action even with `values` being an
array instead of a vector. Consider just the summing loop from the above
example over an array of a structure that wraps an integer:

```rust
#[derive(Debug)]
struct Int(i32);

impl Drop for Int {
    fn drop(&mut self) {
        println!("Int({}) dropped", self.0)
    }
}

fn main() {
    let values = [Int(1), Int(2), Int(3), Int(4), Int(5)];
    let mut sum = 0;

    for value in values {
        println!("value = {value:?}");
        sum += value.0;
    }

    println!("sum = {sum}");
}
```

`Int` implements `Drop` so that a message is printed when an instance get
dropped. Running the above code will print:

    value = Int(1)
    Int(1) dropped
    value = Int(2)
    Int(2) dropped
    value = Int(3)
    Int(3) dropped
    value = Int(4)
    Int(4) dropped
    value = Int(5)
    Int(5) dropped
    sum = 15

It's clear that each value is acquired and dropped while the loop is running.
Once the loop is complete, the sum is printed. If `values` in the `for` loop
is changed to `&values` instead, like this:

```rust
for value in &values {
    // ...
}
```

then the output of the program will change radically:

    value = Int(1)
    value = Int(2)
    value = Int(3)
    value = Int(4)
    value = Int(5)
    sum = 15
    Int(1) dropped
    Int(2) dropped
    Int(3) dropped
    Int(4) dropped
    Int(5) dropped

This time, values are acquired but not dropped while looping because each item
doesn't get owned by the interation loop's variable. The sum is printed ocne
the loop is done. Finally, when the `values` array that still owns all the the
`Int` instances goes out of scope at the end of `main`, its dropping in turn
drops all the `Int` instances.

These examples demonstrate that while iterating collection types may seem to
have a lot of parallels between Rust and Java, from the looping constructs to
the iteration abstractions, there are still subtle differences with respect to
ownership that can lead to the compiler rejecting the code in some instances.

See also:

- [Iterator][iter-mod]
- [Iterating by reference]

[into-iter.rs]: https://doc.rust-lang.org/std/iter/trait.IntoIterator.html
[iter.rs]: https://doc.rust-lang.org/core/iter/trait.Iterator.html
[iter-mod]: https://doc.rust-lang.org/std/iter/index.html
[iterating by reference]: https://doc.rust-lang.org/std/iter/index.html#iterating-by-reference

## Stream Operations

Java's Stream API offers a set of _operations_ (methods) that can be chained together to form stream _pipelines_. In Rust, such methods are called _[adapters]_.

However, while rewriting an imperative loop as a stream in Java is often beneficial in expressivity, robustness and composability, there is a trade-off with performance. Compute-bound imperative loops _usually_ run faster because they can be optimised by the JIT compiler and there are fewer virtual dispatches or indirect function invocations incurred. The surprising part in Rust is that there is no performance trade-off between choosing to use method chains on an abstraction like an iterator over writing an imperative loop by hand. It's therefore far more common to see the former in code.

The following table lists the most common Stream API methods and their approximate
counterparts in Rust.

| Java        | Rust         | Note        |
| ----------- | ------------ | ----------- |
| `reduce`    | `reduce`     | See note 1. |
| `reduce`    | `fold`       | See note 1. |
| `allMatch`  | `all`        |             |
| `anyMatch`  | `any`        |             |
| `concat`    | `chain`      |             |
| `count`     | `count`      |             |
| `max`       | `max`        |             |
| `min`       | `min`        |             |
| `map`       | `map`        |             |
| `flatMap`   | `flat_map`   |             |
| `skip`      | `skip`       |             |
| `limit`     | `take`       |             |
| `takeWhile` | `take_while` |             |
| `collect`   | `collect`    | See note 2. |
| `filter`    | `filter`     |             |


1. Java's `reduce` method is overloaded. The one not accepting an identity value is 
   equivalent to Rust's `reduce`, while the one accepting an identity value corresponds
   to Rust's `fold`.

2. [`collect`][collect.rs] in Rust generally works for any collectible type,
   which is defined as [a type that can initialize itself from an iterator
   (see `FromIterator`)][FromIter.rs]. `collect` needs a target type, which
   the compiler sometimes has trouble inferring so the _turbofish_ (`::<>`) is
   often used in conjunction with it, as in `collect::<Vec<_>>()`.

  [FromIter.rs]: https://doc.rust-lang.org/stable/std/iter/trait.FromIterator.html

The following example shows how similar transforming sequences in Java is to
doing the same in Rust. First in Java:

```java
int result = IntStream.range(0, 10)
                .filter(x -> x % 2 == 0)
                .flatMap(x -> IntStream.range(0, x))
                .reduce(0, Integer::sum);

System.out.println(result); // prints: 50
```

And in Rust:

```rust
let result = (0..10)
    .filter(|x| x % 2 == 0)
    .flat_map(|x| (0..x))
    .fold(0, |acc, x| acc + x);

println!("{result}"); // prints: 50
```

[adapters]: https://doc.rust-lang.org/std/iter/index.html#adapters
[collect.rs]: https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.collect


## Deferred execution (laziness)

Java streams are lazy: computation on the source data is only performed when a 
_terminal operation_ is initiated, and source elements are consumed only as needed. This enables composition or chaining of several (_intermediate_) operations/methods without causing any side-effects. For example, a stream operation can return an `Iterable<T>` that is initialized, but does not produce, compute or materialize any items of `T` until iterated. The operation is said to have _deferred execution_ semantics (or _lazy evaluation_). If each `T` is computed as iteration reaches it (as opposed to when iteration begins) then the operation is said to _stream_ the results.

Rust iterators have the same concept of [_laziness_][iter-laziness] and
streaming.

  [iter-laziness]: https://doc.rust-lang.org/std/iter/index.html#laziness
  [stream]: https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/stream/Stream.html
