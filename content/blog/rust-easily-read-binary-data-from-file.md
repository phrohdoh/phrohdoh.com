+++
title = "easily read all bytes of a binary file in Rust"
path = "blog/rust/easily-read-all-bytes-of-a-binary-file"

draft = false # true
date = 2021-08-26

[taxonomies]
tags = ["rust"]
+++

[Rust's `File` type][rust-file-docs] can be overwhelming.
There is a lot going on.

One might expect it to be obvious how to read in all the bytes of a file. After
all, how difficult can "read it all!" really be?  The problem is,
[there][rust-file-read-1] [are][rust-file-read-2] [many][rust-file-read-3]
[ways][rust-file-read-4]!

This indicates that there is nuance in this particular act.  Nuance that
frankly, **you don't want to deal with** at this stage. You want easy, now.
Get it working then improve it.

At this point you may think "someone else has surely improved this in a
library!" and head off to [crates.io](https://crates.io), and if so **WAIT!**

This can be done with `std` alone, no dependencies necessary.  Throwing nuance
to the wind we can reach for the [`std::fs::read`] function.

```rust
use std::{io, fs};

fn main() -> io::Result<()> {
    let all_bytes = fs::read("path/to/file.txt")?;
    println!("{:x?}", all_bytes);
    Ok(())
}
```

[`std::fs::read`]: https://doc.rust-lang.org/std/fs/fn.read.html
[rust-file-docs]: https://doc.rust-lang.org/std/fs/struct.File.html
[rust-file-read-1]: https://doc.rust-lang.org/std/fs/struct.File.html#method.read
[rust-file-read-2]: https://doc.rust-lang.org/std/fs/struct.File.html#method.read_to_end
[rust-file-read-3]: https://doc.rust-lang.org/std/fs/struct.File.html#method.bytes
[rust-file-read-4]: https://doc.rust-lang.org/std/fs/struct.File.html#impl-Read-1
