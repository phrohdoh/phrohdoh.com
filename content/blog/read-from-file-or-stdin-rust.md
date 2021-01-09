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

and either one of the following invocations

```shell
$ ./your-program path/to/file.csv

# OR

$ cat path/to/file.csv | ./your-program -
```

you want to see all of the lines printed to [stdout] (yep, we're implementing a
[cat]-like).

<br/>

This can be achieved by using a boxed reader, which allows the other parts of
your code to be agnostic to the source of the data (file, stdin, socket, ...),
like so.

```rust
use std::{env, io, fs};

fn main() {
  // file path provided by user
  let input = env::args().nth(1).unwrap();

  // build the boxed reader
  let mut rdr: Box<io::Read> = match input {
    "-" => Box::new(io::stdin()),
    _   => Box::new(fs::File::open(input).unwrap()),
  };

  // write data from reader to stdout
  io::copy(&mut rdr, &mut io::stdout()).unwrap();
}
```

[stdin]: https://en.wikipedia.org/wiki/Standard_streams#Standard_input_(stdin)
[stdin-dash-convention]: https://unix.stackexchange.com/a/16364/355959
[stdout]: https://en.wikipedia.org/wiki/Standard_streams#Standard_input_(stdout)
[cat]: https://en.wikipedia.org/wiki/Cat_(Unix)
