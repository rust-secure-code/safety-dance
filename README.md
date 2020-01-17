# Rust Safety Dance

<img src="https://raw.githubusercontent.com/rust-secure-code/safety-dance/master/img/safety-dance.png" width="320">


## Before you start

Safety is important part of our live.
But some "extreme" improve our live and future.
We are must understand - Nature make some experiments on us, some times too dangerous.
Unsafe rust is field to extreme, and we are need this extream too.

## About

This is a place for people to communicate about auditing `unsafe` code in core
Rust crates and replacing it with safe code where feasible.

**Everyone is invited to participate!**

You **do not** have to be an `unsafe` expert to help out. There's a lot of work
to do just picking crates (ones with a lot of reverse-dependencies are best),
and then sorting out where they use `unsafe` and why. If you think something
isn't right just post it in the tracking issue and others can have a look and
talk it out.

## Process

Our process is as follows:

1) File a tracking issue _in this repo_ about a particular crate, giving its
   name and a link to their github (or other repository location).
2) Audit `unsafe` usage in that crate.
    * This is easy to start! Note that the GitHub search isn't very good, so it's
      best to clone the project and use an editor on your own computer. The
      [cargo geiger](https://github.com/anderejd/cargo-geiger) command can also
      help here.
    * Once you know where the `unsafe` blocks are it gets harder: you have to
      carefully determine if the `unsafe` is being used appropriately. We've been
      [requesting Clippy lints](https://github.com/rust-secure-code/safety-dance/issues/21)
      for known antipatterns, so running `cargo +nightly clippy` is a good
      starting point. If you don't know if a certain `unsafe` block is okay,
      post the questionable block in a comment in the tracking issue here
      and someone else can have a look too, or ask in
      `#black-magic` on [Rust Community Discord](https://discord.gg/aVESxV8).
3) When problems are found with an `unsafe` block we want to file bug reports in
   that crate's repo, send PRs with fixes if possible, and also write up
   [security advisories](https://github.com/RustSec/advisory-db) if necessary.
    * If the `unsafe` block is sound, but can be converted to safe code without
      losing performance, that's a great thing to do! This is often the case
      thanks to Rust adding new safe abstractions and improving the optimizer
      since the code was originally written.
    * It's possible that `unsafe` can't be eliminated without a performance
      loss. Unfortunate, but it will happen some of the time. Note that benchmarks
      _must_ actually be used to back up any performance loss claims. There are
      already many cases where switching from `unsafe` to safe alternatives has
      _increased_ performance, so simply guessing that performance will regress
      is not enough.
    * If switching away from unsafe is impossible because of missing abstractions
      then that's important to know! We can work on improving the language, the
      standard library, and/or the crates.io ecosystem until the necessary gaps
      are filled in.
4) Once a crate has been gone over enough we close that issue. If the crate
   needs re-checking again later on we just open a new issue.
5) (Optional) If you have completely cleansed a crate of `unsafe`, add a
   `#![forbid(unsafe_code)]` attribute to its `src/lib.rs` or `main.rs`.
   After doing that, help others discover Safety Dance by adding a badge to
   your README.md: ![unsafe forbidden](https://img.shields.io/badge/unsafe-forbidden-success.svg)

Markdown code:

```
[![unsafe forbidden](https://img.shields.io/badge/unsafe-forbidden-success.svg)](https://github.com/rust-secure-code/safety-dance/)
```

## 🏆 Trophy Case 🏆

Check out the safety improvements already done!

### [gif](https://crates.io/crates/gif)

GIF image encoder/decoder written in Rust ([tracking issue](https://github.com/rust-secure-code/safety-dance/issues/24))

 - Unsafe blocks before: **6** (ignoring C API)
 - Unsafe blocks after: **2** (ignoring C API)

100% safety blocked by [Polonius integration in rustc](https://github.com/rust-lang/rust/issues/51545)

Done by: [Shnatsel](https://github.com/Shnatsel/)

### [flate2](https://crates.io/crates/flate2)

A streaming compression/decompression library DEFLATE-based streams in Rust. Has C and Rust backends ([tracking issue](https://github.com/rust-secure-code/safety-dance/issues/32))

 - Unsafe blocks before: **21** (when using Rust backend)
 - Unsafe blocks after: **2** (when using Rust backend)
 - Switched to using Rust backend by default (see [miniz_oxide](#miniz_oxide) below)

Done by: [oyvindln](https://github.com/oyvindln/), [Shnatsel](https://github.com/Shnatsel/), [Alex Crichton](https://github.com/alexcrichton)

### [image](https://crates.io/crates/image)

Image operations and conversions to/from image formats ([tracking issue](https://github.com/rust-secure-code/safety-dance/issues/3))

- Unsafe blocks before: **21** (many of them unsound)
- Unsafe blocks after: **6**
- **Security bug fixed: [RUSTSEC-2019-0014](https://rustsec.org/advisories/RUSTSEC-2019-0014.html)**

The remaining unsafe blocks are inherent and cannot be removed. They have been audited and found to be sound.

Done by: [fintelia](https://github.com/fintelia), [HeroicKatora](https://github.com/HeroicKatora), [64](https://github.com/64)

### [libflate](https://crates.io/crates/libflate)

Popular DEFLATE compression/decompression library ([tracking issue](https://github.com/rust-secure-code/safety-dance/issues/1))

- Unsafe blocks before: **16** (4 of them unsound)
- Unsafe blocks after: **1** plus 2 more moved to shared crates
- **Security bug fixed: [RUSTSEC-2019-0010](https://rustsec.org/advisories/RUSTSEC-2019-0010.html)**

100% safety blockers: [rust-lang/rust#59229](https://github.com/rust-lang/rust/issues/59229), [rust-lang/rfcs#2714](https://github.com/rust-lang/rfcs/pull/2714)

Done by: [DevQps](https://github.com/DevQps), [Shnatsel](https://github.com/Shnatsel/), [WanzenBug](https://github.com/WanzenBug/)

### [miniz_oxide](https://crates.io/crates/miniz_oxide)

The fastest DEFLATE compression/decompression library in Rust, backend for [flate2](https://crates.io/crates/flate2) ([tracking issue](https://github.com/rust-secure-code/safety-dance/issues/2))

- Unsafe blocks before: **28** (2 of them unsound)
- **100% safe code now** - while being faster than the C version!
- Potential security issue fixed: [Frommi/miniz_oxide#36](https://github.com/Frommi/miniz_oxide/pull/36) (unclear if exploitable or not)

Done by: [Shnatsel](https://github.com/Shnatsel/), [oyvindln](https://github.com/oyvindln/)

### [spin](https://crates.io/crates/spin)

A spinlock for Rust ([tracking issue](https://github.com/rust-secure-code/safety-dance/issues/18))

- `spin::RwLock` found to be unsound,completely rewritten based on Facebook's [Folly](https://github.com/facebook/folly) implementation, new implementation audited for soundness
- **Security bug fixed: [RUSTSEC-2019-0013](https://rustsec.org/advisories/RUSTSEC-2019-0013.html)**

Done by: [64](https://github.com/64), [xacrimon](https://github.com/xacrimon)

`spin::Once` still needs to be audited for soundness, see [tracking issue](https://github.com/rust-secure-code/safety-dance/issues/18)

## We need your help!

You can help by:

1. Nominating crates for auditing - we're looking for widely used crates with `unsafe` in them
1. Auditing nominated crates for soundness
1. Replacing unsafe code with safe code where possible (where not possible - documenting why)
1. Inspecting crates that have been made safer and [requesting Clippy lints](https://github.com/rust-secure-code/safety-dance/issues/21) for the antipatterns discovered

Check out what's in progress or pick up a work item on the [issue tracker](https://github.com/rust-secure-code/safety-dance/issues)!
