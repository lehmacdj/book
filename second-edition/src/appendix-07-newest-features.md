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

Unions provide a means of interpreting the same bits in different ways.
Currently only types implementing `Copy` and not `Drop` can be used in an
`union`.

```rust
union MyUnion {
    float: f32,
    int: u32,
}
```

The fields of a `union` allow you to interpret the data as one specific type
even if it isn't the same one it was set as. As a result, reading and writing
from `union` fields is unsafe:

```rust
let u = MyUnion { int: 10 };

unsafe { u.float = 3.14 };

let value = unsafe { u.int };
```

Pattern matching can also be used. This allows only matching against a specific
variant if the value for that field matches a specific value:

```rust
fn f(u: MyUnion) {
    unsafe {
        match u {
            MyUnion { int: 10 } => { println!("ten"); }
            MyUnion { float: f } => { println!("{}", f); }
        }
    }
}
```

Unions are useful for interfacing with C APIs that expose unions and for cases
where manual casting would be too error prone otherwise.
