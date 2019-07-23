# Rust Safety Dance

<img src="https://raw.githubusercontent.com/rust-secure-code/safety-dance/master/img/safety-dance.png" width="320">

## About

This is a place for people to communicate about `unsafe` code in Rust crates.

Our process is as follows:

1) File a tracking issue _in this repo_ about a particular crate, giving its
   name and a link to their github (or other repository location).
2) Audit `unsafe` usage in that crate.
  * This is easy to start! Note that the GitHub search isn't very good, so it's
    best to clone the project and use an editor on your own computer. The
    [cargo geiger](https://github.com/anderejd/cargo-geiger) command can also
    help here.
  * Once you know where the `unsafe` blocks are it gets harder: you have to
    carefully determine if the `unsafe` is being used appropriately. If you
    don't know that's okay, post the questionable block in a comment in the
    tracking issue here and someone else can have a look too.
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
    already many cases where switching from `unsafe` to safe alternateives has
    _increased_ performance, so simply guessing that performance will regress
    is not enough.
  * If switching away from unsafe is impossible because of missing abstractions
    then that's important to know! We can work on improving the language, the
    standard library, and/or the crates.io ecosystem until the necessary gaps
    are filled in.
4) Once a crate has been gone over enough we close that issue. If the crate
   needs re-checking again later on we just open a new issue.
5) (optional) If you have completely cleansed a crate of `unsafe`, add a
   `#![forbid(unsafe_code)]` attribute to its `src/lib.rs` or `main.rs`
   and/or an ![Safety Dance](https://img.shields.io/badge/unsafe-forbidden-success.svg)
   badge to its README.md. Markdown:

```
[![Safety Dance](https://img.shields.io/badge/unsafe-forbidden-success.svg)](https://github.com/rust-secure-code/safety-dance/)
```

**Everyone is invited to participate!**

You **do not** have to be an `unsafe` expert to help out. There's a lot of work
to do just picking crates (ones with a lot of reverse-dependencies are best),
and then sorting out where they use `unsafe` and why. If you think something
isn't right just post it in the tracking issue and others can have a look and
talk it out.
