+++
title = "sending synchronous HTTP requests in Rust"
date = 2020-12-12
draft = false
+++

You're building a proof-of-concept, not launching satellites. It should be easy
to send an HTTP request but every example you see makes you more confused
because of the unexplained use of `async`/`await`!

Is using `async`/`await` _required_? If so, what exactly is requiring it?
The library? The language? What's with the `.await?` syntax? How much do you
have to learn before you can just get something working?! 😠

All of this asynchronous stuff can be confusing, so you'd rather not worry about
it and just send synchronous requests. How can you do this?

<br/>

The [reqwest] library can be used with or [without] `async`/`await`. To do so
without, the `blocking` [feature] must be enabled. Ensure the project's manifest
(that's the _Cargo.toml_ file) contains the following.

```toml
[dependencies]
reqwest = { version = "^0.10", features = ["blocking"] }
```

Then in the code, the request can be created and sent like so.

```rust
use reqwest::blocking::Client;

fn main() {
    let request = Client::new()
        .get("https://example.com/search_result")
        .query(&[("sort", "name"), ("limit", "8")])
        .bearer_auth("1q2w3e4r5t6y7u8i9o0p")
        .header("Connection", "close");

    let result = request.send();

    match result {
        Ok(response) => println!("{}", response.status()),
        Err(err) => todo!(),
    }
}
```

[reqwest]: https://docs.rs/reqwest/
[without]: https://github.com/seanmonstar/reqwest#blocking-client
[feature]: https://doc.rust-lang.org/cargo/reference/features.html
