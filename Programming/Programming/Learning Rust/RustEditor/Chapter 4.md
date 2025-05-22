## 4. A text editor

* Vectors `Vec<Row>` are dynamic structure that hold the same type in this case `Row`
* Using the directive `#[derive(Default)]` we can tell the compiler to derive a default method for us. This only works for simple structs (No derived types).
* When using `impl From<&str> for Row` we implement the "From" trait for our Row struct 
* `std::fs` contains functions and method for interacting with the filesystem (including reading files)
* Opening a file and reading its input can be achieved with this snippet: 

```rust
use std::fs;

fn main {
  let filename = "test.txt";
  let content = fs::read_to_string(filename);
  for ln in content.lines(){
    println!("{}", ln);
  }
}
```
* You can assign to `if..else` by using the syntax
```rust
let var: usize = if cond {
  //Do stuff
  1
} else {
 //Do more stuff
 2
};
```
* By omitting the `;` semicolon in the last line of the statements and by adding `;` after else (Remember Semicolons mark a statement in Rust, this is what we want here)
.