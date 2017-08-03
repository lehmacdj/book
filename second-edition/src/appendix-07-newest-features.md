# Appendix G - Newest Features

This appendix documents features that have been added to stable Rust since the
main part of the book was completed.


## Field init shorthand

We can initialize a data structure (struct, enum, union) with named
fields, by writing `fieldname` as a shorthand for `fieldname: fieldname`.
This allows a compact syntax for initialization, with less duplication:

```rust
#[derive(Debug)]
struct Person {
    name: String,
    age: u8,
}

fn main() {
    let name = String::from("Peter");
    let age = 27;

    // Using full syntax:
    let peter = Person { name: name, age: age };

    let name = String::from("Portia");
    let age = 27;

    // Using field init shorthand:
    let portia = Person { name, age };

    println!("{:?}", portia);
}
```


## Returning from loops

One of the uses of a `loop` is to retry an operation you know can fail, such as
checking if a thread completed its job. However, you might need to pass the
result of that operation to the rest of your code. If you add it to the `break`
expression you use to stop the loop, it will be returned by the broken loop:

```rust
fn main() {
    let mut counter = 0;

    let result = loop {
        counter += 1;

        if counter == 10 {
            break counter * 2;
        }
    };

    assert_eq!(result, 20);
}
```

## Unions

Unions provide a means of interpreting the same underlying bits with different
types without type casts. Right now only types implementing `Copy`
(and not `Drop`) can be fields in a `union`. Accessing a field of a `union`
interprets the value stored in the union as having the specified
type. Writing to a field puts a value there such that reading it later from the
same field will have the same value. You can also pattern match on `union`s.
Unless constants restrict the pattern to match only in certain cases, the match
statement will match any fields from the union.

```rust
union MyUnion {
    float: f32,
    int: u32,
}

fn main() {
    let mut u = MyUnion { int: 10 };
    let value1 = unsafe { u.int };
    assert_eq!(10, value1);

    u.float = 3.14;

    // prints "3.14"
    unsafe {
        match u {
            MyUnion { int: 10 } => { println!("ten"); }
            MyUnion { float: f } => { println!("{}", f); }
        }
    };
}
```
