Based on https://doc.rust-lang.org/book/ you can always open a local copy
of the rust book by using `rustup docs --book`
# Gettings Started

* Rust files &Rightarrow; end with ```.rs```
* Compile with ```rustc main.rs```, Execute with ```./main``
* Use underscore to seperate words in filenames
* Lines end with ";"
* Compiled language &Rightarrow; Compiling and running are seperate steps

# Rust Build System: Cargo

* Handle dependencies, download packages, setup projects

__Setup Project__:

```
cargo new project_name 
```
* Will create a folder with a .toml file and a src directory with a main file
* Is initalised with a new git repository, can be changed to other vc &Rightarrow; see cargo help

__Typical cargo commands__: 

* `cargo build` &Rightarrow; Compile project, create folder with executable `target/debug/project_name` use `--release` flag to build release version
* `cargo run` &Rightarrow; Run project once it was vuild
* `cargo check` &Rightarrow; Check project but does not produce executable, faster than build thus can be used more often

# Crate (External Modules)

A crate is a collection of rust source code files. A crate can either be a _binary crate_, which is an executable or a _library crate_, which is code intended to be used in other programs. 

To include external crates that are in the cargo repository we need to modify the `Cargo.toml` file. We need to add the crate to the dependency. For example if we want to add the rand crate to the project

```toml
[dependencies]
rand = "0.5.5"
```

Note that "0.5.5" is shorthand for "^0.5.5", which is _SemanticVersioning_ and means a version at least 0.5.5 but below 0.6.0

Now if you run `cargo build` all dependencies will be fetched automatically and are compiled so that they can be used. 

The `cargo.lock` file handles that the version is fixed at 0.5.5 and the code does not need to be changed. 

With `cargo update` all dependencies can be updated (if they are changed in the dependencies entry in the cargo.toml file).

## Open documentation of your local crates

The `cargo doc --open` command will build documentation provived by all dependencies locally and open it in your browser. 

# Basics

## Variables and Mutability

# Why Rust

Rust is a statically typed compiled language without a garbage collector. Because of this design decisions rust is fast, memory and thread safe as well as reliable making it one of the 

# Rust in vs code

* In 2022 the recommended rust extension is the **rust-analyzer**, and no longer *rls*.

More on this topic here: https://code.visualstudio.com/docs/languages/rust

* You can format your code by running `STRG + SHIFT + P` and type in format document. On the first run you may need to select a debugger we either recommend rust-analyzer or prettier for that


# Crash Course by Traversy Media

**General Information**:

* He will create a new file for every topic he will cover in his video

## Initiaize a project and compile files

Generally you almost always want to use cargo for this job which is rusts build and package management system:

* `cargo init`:  Creates a new cargo project in an existing folder
* `cargo new package_name`: Creates a new package with a new folder package_name
* `cargo build`: Builds a package
* `cargo build --release`: Will build with optimized version
* `cargo run`: Builds ans runs a package

All your dependencies and meta information are handled inside the `cargo.toml`file. 
More on this topic here: https://doc.rust-lang.org/cargo/

## Rust sandbox

Using `cargo run` will create the binary inside ./target/debug/nameofpackage

# Further ressources

* The most comprehensive guide is the official book https://doc.rust-lang.org/book/index.html
* Learning rust by example https://doc.rust-lang.org/rust-by-example/index.html is similiar to go by example. Teachning you typical patterns with practical examples
* Rustlings. Learn rust by small excercices https://github.com/rust-lang/rustlings
* Learn rust in 30 minutes https://fasterthanli.me/articles/a-half-hour-to-learn-rust


# Editor setup for Rust

## VS Code

* Install rust-lang
* Install lldb as debugger for rust 