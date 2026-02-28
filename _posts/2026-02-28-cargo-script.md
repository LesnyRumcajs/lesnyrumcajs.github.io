---
layout: post
title:  "We now have cargo-script!"
date:   2026-02-28 18:00:00 +0100
categories: programming
tags: [rust]
---

> The `cargo-script` is not yet stabilised, but it is already available in the nightly Rust. If you're reading this a few months after this post's publication, it might already be stabilised, with slightly different syntax. Best to follow the [stabilisation issue in rust-lang repo](https://github.com/rust-lang/cargo/pull/16569).
{: .prompt-info }

## What is cargo-script?

2026 started with a very exciting feature coming to Rust: the `cargo-script`. It allows you to run Rust code like a script, without the usual boilerplate of a fully-fledged Rust project with `Cargo.toml`, `src` directory and such. Can't wait to try it out? Let's see how to use it!

### First look 

To use this, you don't need to create a new Rust project (THE SHOCK!). Create a regular Rust file, for example `hello.rs`, and add the following code:

```rust
#!/usr/bin/env -S cargo +nightly -Zscript --quiet

fn main() {
    println!("Cthulhu fhtagn!");
}
```

> For now, you need to use the nightly Rust and enable the experimental `script` feature. The `--quiet` flag is optional; it just suppresses, e.g., compilation messages. In my mind, it somehow matches how scripts usually work.
{: .prompt-tip }

Now, after `chmod +x hello.rs`, you can run it with `./hello.rs` and see the output:

```console
$ ./hello.rs
Cthulhu fhtagn!
```

You can also run it with `cargo ...`, without making it executable and shebang tweaks:
```console
cargo +nightly --quiet -Zscript hello.rs
```

That's... nice. But not really impressive. We could achieve the same with `rustc hello.rs; ./hello`, without shebang and the nightly Rust gymnastics. But wait, there's more!

### Embedding dependencies

Say you want to share a code snippet on the internet, perhaps a bug reproduction, or a working code example. You probably want to include some dependencies. Some people might be a bit hesitant to download your `zip` file with the whole project, but would a single file that could be easily copy-pasted, e.g., via GitHub Gist, be better? Probably!

Another use case is just running some quick code from a crate's `README`. Let's take my [wakey](https://github.com/LesnyRumcajs/wakey) crate as an example. Perhaps you want to quickly test it out and see if it wakes up some of your devices. You could do it like this:

```rust
#!/usr/bin/env -S cargo +nightly -Zscript --quiet

---cargo
[dependencies]
wakey = "0.3"
---

fn main() -> Result<(), Box<dyn std::error::Error>> {
    use std::net::SocketAddr;

    let wol = wakey::WolPacket::from_bytes(&[0x00, 0x01, 0x02, 0x03, 0x04, 0x05])?;
    let src = SocketAddr::from(([0, 0, 0, 0], 0));
    let dst = SocketAddr::from(([255, 255, 255, 255], 9));

    wol.send_magic_to(src, dst)?;
    Ok(())
}
```

All done in a single file! You probably recognise the syntax for embedding dependencies from your regular `Cargo.toml`. It's put in a frontmatter block, usually found in markdown files.

Perhaps the code you just ran doesn't work as expected; now you can easily copy-paste your MRE into the crate's issue tracker. Not a big deal if it's an easy reproduction, but if it requires some more complex setup and other dependencies, this approach is significantly cleaner. As a maintainer of several open-source Rust crates, I wholeheartedly approve this feature.


### Limitations

There are quite a few limitations of `cargo-script` in its current state; some are expected to be lifted as the feature stabilises, and some are not blockers, so we'll see how they evolve. No point in me listing them all here, as it'd be a copy-paste of the [tracking issue](https://github.com/rust-lang/cargo/pull/16569). Be sure to check it out.

An obvious limitation that's not listed there is that your LSP will think you're bonkers for starting a Rust file with a shebang!

### Final thoughts

I'm looking forward to seeing how this feature evolves and how the Rust community will use it. I hope there will be a feature to kind of _zip_ your project into a single file, with all the dependencies, code, compiler flags, and such, so one can easily provide a single-file reproduction of a bug or just share a complete code example.
