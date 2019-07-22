# Rust Safety Dance

<img src="https://raw.githubusercontent.com/rust-secure-code/safety-dance/master/img/safety-dance.png" width="320">

## About

This is a place for people to communicate about `unsafe` code in Rust crates.

Our process is as follows:

1) File a tracking issue _in this repo_ about a particular crate, giving its
   name and a link to their github (or other repository location).
2) Audit `unsafe` usage in that crate.
  * This is easy to start! You can use a github repo search for the `unsafe`
    keyword or clone the repo and use your own editor's search.
  * Once you know where the `unsafe` blocks are it gets harder: you have to
    carefully determine if the `unsafe` is being used appropriately. If you
    don't know that's okay, post the questionable block in a comment in the
    tracking issue here and someone else can have a look too.
3) When problems are found with an `unsafe` block we want to file bug reports in
   that crate's repo, send PRs with fixes if possible, and also write up
   security advisories if necessary.
4) Once a crate has been gone over enough we close that issue. If the crate
   needs re-checking again later on we just open a new issue.

**Everyone is invited to participate!**

You **do not** have to be an `unsafe` expert to help out. There's a lot of work
to do just picking crates (ones with a lot of reverse-dependencies are best),
and then sorting out where they use `unsafe` and why. If you think something
isn't right just post it in the tracking issue and others can have a look and
talk it out.
