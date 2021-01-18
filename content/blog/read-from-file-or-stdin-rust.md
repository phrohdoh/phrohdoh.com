+++
title = "reading from a file or stdin in Rust"
date = 2021-01-09

[taxonomies]
tags = ["rust"]
+++

Your command-line application reads from a file path supplied by the user and in
an effort to be maximally useful, you've decided it should have the ability to
read from [stdin] (when the [user provides `-` as the file path][stdin-dash-convention]).

Given the following `path/to/file.csv`

```csv
date,amount,memo
2020-12-14,-500.00,PS5 from Joe's Game Center
```

and either one of these invocations

```shell
$ ./your-program path/to/file.csv

# OR

$ cat path/to/file.csv | ./your-program -
```

you want to see all of the lines printed to [stdout] (yep, implementing a
[`cat`]-like while using `cat` in the example 🤷).

<br/>

Given both [`io::stdin`] and [`fs::File::open`] return types that can be read
from (this is known because the types (after error handing) implement [`io::Read`]),
you may expect to be able to do something like the following.

```rust
use std::{env, io, fs};

fn main() {
  // file path provided by user
  let input = env::args().nth(1).unwrap();

  // get the thing we can read from
  let mut rdr = match input.as_ref() {
    "-" => io::stdin(),
    _   => fs::File::open(input).unwrap(),
  };

  // write data from reader to stdout
  io::copy(&mut rdr, &mut io::stdout()).unwrap();
}
```

But this will [fail to compile][rust-playground-non-boxed-rdr]!

```
error[E0308]: `match` arms have incompatible types
  --> src/lib.rs:10:12
   |
8  |     let mut rdr = match input.as_ref() {
   |  _________________-
9  | |     "-" => io::stdin(),
   | |            ----------- this is found to be of type `Stdin`
10 | |     _   => fs::File::open(input).unwrap(),
   | |            ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ expected struct `Stdin`, found struct `File`
11 | |   };
   | |___- `match` arms have incompatible types

error: aborting due to previous error
```

The compiler cannot determine the appropriate type for `rdr` since in one arm
it is being assigned to a `Stdin` and in the other a `File`. What is needed here
is a type that allows the post-reading code to be agnostic to the source of the
data (file, stdin, socket, ...).

[`Box<io::Read>`][boxed] is this story's hero.
3 quick changes (all `Box`-related) et voilà!

```rust
use std::{env, io, fs};

fn main() {
  // file path provided by user
  let input = env::args().nth(1).unwrap();

  // get the generic thing we can read from
  let mut rdr: Box<io::Read> = match input {
    "-" => Box::new(io::stdin()),
    _   => Box::new(fs::File::open(input).unwrap()),
  };

  // write data from reader to stdout (or whatever you do with it)
  io::copy(&mut rdr, &mut io::stdout()).unwrap();
}
```

[stdin]: https://en.wikipedia.org/wiki/Standard_streams#Standard_input_(stdin)
[stdin-dash-convention]: https://unix.stackexchange.com/a/16364/355959
[stdout]: https://en.wikipedia.org/wiki/Standard_streams#Standard_input_(stdout)
[`cat`]: https://en.wikipedia.org/wiki/Cat_(Unix)
[`io::stdin`]: https://doc.rust-lang.org/std/io/fn.stdin.html
[`fs::File::open`]: https://doc.rust-lang.org/std/fs/struct.File.html#method.open
[`io::Read`]: https://doc.rust-lang.org/std/io/trait.Read.html
[rust-playground-non-boxed-rdr]: https://play.rust-lang.org/?version=stable&mode=debug&edition=2018&gist=99e9de9ec3f9d45e3f2994e0aa7ea8b4
[boxed]: https://doc.rust-lang.org/stable/std/boxed/index.html

<!-- author self-note:
  link final code block to explaine.rs snippet explaining type annotations
  see https://github.com/jrvidal/explaine.rs/issues/13
-->
