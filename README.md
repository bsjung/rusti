# Rusti

A REPL for the Rust programming language.

Rusti is a work in progress.

## Building

Rusti builds with the latest Rust nightly, using the Cargo build system.

Rusti requires GNU Readline.

Build with Cargo:

    cargo build

Run tests:

    cargo test

## Usage

Running `rusti` gives a prompt that accepts (most) any valid Rust code.
If the final statement is an expression, the result will be displayed.

```rust
rusti=> println!("Hello, world!");
Hello, world!
rusti=> 2u + 2
4
rusti=> range(0u, 10).collect::<Vec<_>>()
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
```

If any delimiters are left open, `rusti` will continue reading input until they are closed.
Only then will the code be executed.

```rust
rusti=> fn factorial(n: uint) -> uint {
rusti.>     match n {
rusti.>         0 => 0,
rusti.>         1 => 1,
rusti.>         n => n * factorial(n - 1),
rusti.>     }
rusti.> }
rusti=> factorial(3)
6
rusti=> factorial(4)
24
rusti=> factorial(5)
120
```

## Commands

These are special inputs interpreted by `rusti` that are not directly
evaluated as Rust code, though they may operate on Rust code.

Commands are invoked by entering a line beginning with `.`, followed by the
name of the command and, perhaps, some text used by the command.

Command names may be arbitrarily abbreviated.  
For example, `.type` may be abbreviated as `.typ`, `.ty`, or `.t`.

### `.block`

The `.block` command will run multiple lines of Rust code as one program.

To end the command and run all code, input `.` on its own line.

```rust
rusti=> .block
rusti+> let a = 1i;
rusti+> let b = a * 2;
rusti+> let c = b * 3;
rusti+> c
rusti+> .
6
```

Entering `.q` instead will end the command without running code.

### `.type`

The `.type` command will display the type of an expression without running it.

```rust
rusti=> .type 42i
42i = int
rusti=> .t 'x'
'x' = char
rusti=> .t "Hello!"
"Hello!" = &str
rusti=> .t (1i, 2u)
(1i, 2u) = (int, uint)
rusti=> fn foo() -> int { 1 }
rusti=> .t foo
foo = fn() -> int
rusti=> .t foo()
foo() = int
```

## Limitations

Currently, Rusti has the following limitations.
I hope to fix each of them, but some may prove to be large problems to tackle.

* Functions and types are redefined in each round of input.  
  This is inefficient.
* `static` items are also redefined in each round of input.  
  This means that the address of a `static` item will change in every round
  of input and that the values of `mut` items or those with interior mutability
  will be reset to their initial definition on each round of input.  
  This is bad.
* Some uses of thread-local storage will cause a crash.  
  This includes channels, some functions within `std::task`,
  and likely others elsewhere.  
  This is bad.
* `let` declarations are local to the input in which they are defined.  
  They cannot be referenced later and are destroyed after that round of input
  completes its execution.  
  This is inconvenient.
* And more!

## License

Rusti is distributed under the terms of both the MIT license and the
Apache License (Version 2.0).

See LICENSE-APACHE and LICENSE-MIT for details.
