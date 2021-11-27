# Rust Scratch

## Variables and Mutability

mutability
constants：
1. can't use `mut` with a constants
2. can be declared in any scope
3. may only set to a constant expression, not the result of function
   call or any other value could only be computed at runtime
shadow: declare a new variable with the same name as the previous variable,
        say that the first variable is shadowed by the second, which means
        that the second variable's value is what appears when the variable
        is used.

1. compile-time error when reassign a variable without `let` keyword
2. can change the type of the value but reuse the same name


## data type

syntax:

```rust
let var: <type> = value;
```

### scalar type

A single value is a scalar type

1. integers

* integer types

| Length  | Signed |	Unsigned |
| ------- | ------ | --------- |
| 8-bit   | `i8`   |  `u8`     |
| 16-bit  | `i16`  |  `u16`    |
| 32-bit  | `i32`  |  `u32`    |
| 64-bit  | `i64`  |  `u64`    |
| 128-bit | `i128` |  `u128`   |

* integer literals

| Number literals | Example      |
| --------------- | ------------ |
| Decimal         | 98_222       |
| Hex             | 0xff         |
| Octal           | 0o77         |
| Binary          | 0b1111_0000  |
| Byte (u8 only)  | b'A'         |


rust use the term `panicking` when a program exits with an error

2. floating-point

* `f32`: 32 bits
* `f64`: 64 bits

3. Numeric Operations

* `+`: addition
* `-`: subtraction
* `*`: multiplication
* `/`: division
* `%`: remainder

4. boolean

specified by using `bool`

* `true`
* `false`

4. characters

specified by using: `char`

### compound type

Group multiple values into one type. Two primitive types in rust: `tuple` & `array`

1. tuple

Fixed length: Once declared, cannot grow or shrink in size

* destructuring:

e.g.

```rust
fn main() {
  let tup = (500, 6.4, 1);

  let (x, y, z) = tup;

  println!("The value of y is: {}", y);
}
```

* indexed by `.`, started at 0.

e.g.

```rust
fn main() {
  let x: (i32, f64, u8) = (500, 6.4, 1);

  let five_hundred = x.0;

  let six_point_four = x.1;

  let one = x.2;
}
```

2. array

Every element of an array must have the same type. Also have a fixed length, like tuples.

`let a = [3; 5];` is equivalent to `let a = [3, 3, 3, 3, 3];`

access elements by `[index]`, where index is start at 0 like other languages.


## Functions

syntax:

```rust
fn func-name () {
  func-body
}
```

`main` is the entry point of a rust program

e.g.

```rust
fn main() {
  println!("Hello, world!");

  another_function();
}

fn another_function() {
  println!("Another funciton.");
}
```

### Function Parameters

e.g.

```rust
fn main() {
  another_function(5);
}

fn another_function(x: i32) {
  println!("The value of x is: {}", x);
}
```

#### Statements and Expressions

* Statements: instructions that perform some action and do not return a value
* Expressions: evaluate to a resulting value

cannot assign a `let` statement to another variable, wrong code:

```rust
fn main() {
  let x = (let y = 6); // error when compiling
}
```

block in `{}` is an expression

e.g.

```rust
fn main() {
  let x = 5;

  let y = {
    let x = 3;
    x + 1
  };

  println!("The value of y is: {}", y); // result is 4
}
```

### functions with Return Values

declare return values' type after an arrow (`->`), return the last expression implicitly,
also can return early with `return` keyword.

```rust
fn five() -> i32 {
  5 // can not add a semicolon, otherwise will get a error
}

fn main() {
  let x = five();

  println!("The value of x is: {}", x);
}
```

### Comments

using `//`


## Control Flow

### `if/else/else if` expression

branch the code depending on conditions, which must be `bool` type,
if the condition isn't a `bool`, will get an error

```rust
fn main() {
  let x = 3;

  if x < 5 {
    println!("condition is true");
  } else {
    println!("condition is false");
  }
}
```

using `if` in a `let` statement, values in each arm must be the same type

```rust
fn main() {
  let condition = true;
  let number = if condition { 5 } else { 6 };

  println!("The value of number is: {}", number);
}
```

### Repetition with Loops

#### `loop`

an infinite loop example:

```rust
fn main() {
  loop {
    println!("again!");
  }
}
```

returning values from loops

```rust
fn main() {
  let mut counter = 0;

  let result = loop {
    count += 1;

    if counter == 10 {
      break counter * 2;
    }
  };

  println!("The result is {}", result);
}
```

#### `while`

When the condition cases to be true, the program calls `break`, stopping the loop

```rust
fn main() {
  let mut number = 3;

  while number != 0 {
    println!("{}!", number);

    number -= 1;
  }

  println!("LIFTOFF!!!");
}
```

#### `for`

a safer way to get each element of a collection

```rust
fn main() {
  let a = [10, 20, 30, 40, 50];

  for element in a.iter() {
    println!("the value is: {}", element);
  }
}
```


## Ownership

Rust's central feature

All programs have to manage the way they use a computer's memory while running:
some language manage memory by using garbage collection,
others require programmer explicitly allocate and free the memory.
memory is managerd through a system of ownership with a set of rules
that the compiler checks at compile time in Rust.

### Onwership rules

* Each value in Rust has a variable that's called its owner
* There can only be one owner at at time
* When the owner goes out of scope, the value will be dropped

varialbe scope devided by `{` `}`, in another word, every `{}` is individual scope.

When a variable goes out of scope, Rust calls a special function for us.
This function is called `drop`, and it's where the author of `String` can
put the code to return the memory. Rust calls `drop` automatically at the
closing curly bracket.


string on heap, using point, and if two point to one data, the latter point owns
the data. can duplicate data with clone

```rust
let s1 = String:from("hello");
let s2 = s1.clone();
```


stack-only data using copy instead of clone
cause these data type have certain memory spaces.

Here are the types:

* All the integer types, such as `u32`
* The Boolean type, `bool` with values `true` and `false`
* All the floating point types, such as `f64`
* The character type, `char`
* Tuples, if they only contain types that also implement `Copy`. For example, `(i32, i32)`
  implements `Copy`, but `(i32, String)` does not.


Passing a varaible to a function will move or copy, just as assignment does.

```rust
fn main() {
  let s = String::from("hello");  // s comes into scope

  take_ownership(s);             // s's value moves into the function...
                                 // ... and so is no longer valid here

  let x = 5;                     // x comes into scope

   makes_copy(x);                // x would move into the function,
                                 // but i32 is Copy, so it's okay to still
                                 // use x afterward
} // Here, x goes out of scope, then s. But because s's value was moved, nothing
  // special happends.

```

Returning values can also transfer ownership:

```rust

fn main() {
  let s1 = gives_ownership();        // gives_ownership moves its return
                                     // value into s1
  let s2 = String::from("hello");    // s2 comes into scope

  let s3 = takes_and_gives_back(s2); // s2 is moved into
                                     // takes_and_gives_back, which also
                                     // move its return value into s3
} // Here, s3 goes out of scope and is dropped. s2 goes out of scope but was
  // moved, so nothing happens. s1 goes out of scope and is dropped.

fn gives_ownership() -> String {           // gives_ownership will move its
                                           // return value into the function
                                           // that calls it

  let some_string = String::from("hello"); // some_string comes into scope

  some_string                              // some_string is returned and
                                           // moves out to the calling
                                           // function
}

// takes_and_gives_back will take a String and return one
fn takes_and_gives_back(a_string: String) -> String { // a_string comes into
                                                      // scope

  a_string  // a_string is returned and moves out to the calling function
}
```

## References and Borrowing


### Reference

allow you refer to some value without taking ownership of it
opposite of referencing by using `&` is _dereferencing_ :`*`
by default, references are immutable.

Personall opinion: the meaning is the same as C

```rust
fn main() {
  let s1 = String::from("hello");

  let len - calculate_length(&s1);

  println!("The length of '{}' is {}.", s1, len);
}

fn calculate_length(s: &String) -> usize {
  s.len()
}
```

using `&mut String` to make a reference mutable, but notice
that there's a big restriction: you can have only one mutable
reference to a particular piece of data in a particular scope.

```rust
fn main() {
  let mut s = String::from("hello");

  change(&mut s);
}

fn change(some_string: &mut String) {
  some_string.push_str(", world");
}

```

but the code below will failed:

```rust
let mut s = String::from ("hello");

let r1 = &mut s;

let r2 = &mut s;

println!("{}, {}", s1, s2);
```

However, we could use curly tbrackets `{}` to create a new scope:

```rust
let mut s = String::from("hello");

{
  let r1 = &mut s;
} // r1 goes out of scope here, so we can make a new reference with no problems.

let r2 = &mut s;
```


### slice type

slice does not have ownership

string literals are slices

```rust
let s = String::from("hello");

let len = s.len();

let slice = &s[3..len];
let slice = &s[3..];

let str =  "hello"; // this is string is a slice type
```
