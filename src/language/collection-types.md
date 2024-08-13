# Collection Types

Commonly used collection types in Rust and their mapping to Java:

| Rust         | Java         | Java Interface |  Note       |
| ------------ | ------------ |--------------- |------------ |
| `Array`      | `Array`      |                | see Note 1. |
| `Vec`        | `ArrayList`  | `List`         |             |
| `HashMap`    | `HashMap`    | `Map`          |             |
| `HashSet`    | `HashSet`    | `Set`          |             |
| `Tuple`      |              |                | see Note 2. |
| `LinkedList` | `LinkedList` | `List`         | see Note 3. |


Notes:

1. Java provides the `Arrays` utility class for manipulating arrays.

2. Unlike Rust, Java does not have the `Tuple` type.

3. In both Rust and Java the `LinkedList` collection is implemented using a doubly-linked
    list.

## Arrays

Fixed arrays are supported the same way in Rust and Java.

Java:

```java
int[] someArray = new int[] { 1, 2 };
```

Rust:

```rust
let some_array: [i32; 2] = [1,2];
```

Notice the type of `some_array`: `[T; N]` - it indicates both the type of elements of the array and the size of the array (which is fixed at compile time).

Accessing array elements is similar in both languages:

Java:

```java
int firstElement = someArray[0];
int secondElement = someArray[1];

System.out.println(firstElement);  // prints: 1
System.out.println(secondElement); // prints: 2
```

Rust:

```rust
let first_element = some_array[0];
let second_element = some_array[1];

println!("{}", first_element);  // prints: 1
println!("{}", second_element); // prints: 2
```

## ArrayList

In Rust, the equivalent of Java's `ArrayList<E>` is `Vec<T>`. Arrays can be converted
to Vecs and vice versa.

Java:

```java
List<String> someList = new ArrayList<>(Arrays.asList("a", "b"));

someList.add("c");
```

Rust:

```rust
let mut some_list = vec![
    "a".to_owned(),
    "b".to_owned()
];

some_list.push("c".to_owned());
```


## HashMap

In both Rust and Java, a HashMap is represented as `HashMap<K, V>`.

Java:

```java
Map<String, String> someMap = new HashMap<>(Map.of("Foo", "Bar", "Baz", "Qux"));

someMap.put("hi", "there");
```

Rust:

```rust
let mut some_map = HashMap::from([
    ("Foo".to_owned(), "Bar".to_owned()),
    ("Baz".to_owned(), "Qux".to_owned())
]);

some_map.insert("hi".to_owned(), "there".to_owned());
```

## HashSet

In Rust, the equivalent of Java's `HashSet<E>` is `HashSet<T>`.

Java:

```java
Set<String> someSet = new HashSet<>(Set.of("a", "b"));

someSet.add("c");
```

Rust:

```rust
let mut some_set = HashSet::from(["a".to_owned(), "b".to_owned()]);

some_set.insert("c".to_owned());
```


See also:

- [Rust's standard library - Collections](https://doc.rust-lang.org/std/collections/index.html)
