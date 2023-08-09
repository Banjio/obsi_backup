## 3. Raw input and output

**Idiomatic Rust code**

* Instead of reading bytes, we should read keys (Because reading bytes usually means you are to low level solving a problem someone else already solved in a library) &Rightarrow; In this case `termion`
* `_` as tree in a match statement handley every case that is not matched so far
* In Rust (as in every other programming language) the main function should only be the entry point of the app and should contain the least code possible. All other things are handled in different files, so that the programm is easier maintainable
* A `struct` is a collection of variables and eventually functions forming an entity (Similiar to pythons `class`). A big advantage of structs is that we can use several declarations like `pub, private, etc` to say where the struct should be accessible
* Implementing functions for a struct is done using the `impl` keyword
* `&self` is a reference to the struct a function was called upon &Rightarrow; `&` is the keyword for references
* **Clippy** is an awesome tool for analysing your code: 
  * Run `cargo clean` to make sure clippy analyses the whole code (Because we learnt cargo build only looks for changes)
  * Use `cargo clippy -- -W clippy::pedantic` to get an analysis of your code where clippy is very strict (Leave the flag if you do not want this)
* The `loop` keyword can be used to start an infinite loop
* `if let` is a shortcut for match where only one case should be handled andall others ignored
* Writing `?` after a function call means we want to stop here and return with an error (if one occurs in the called function) or else continue. 
* In rust we do not want to panic somewhere deep inside our code, instead we want to end at the highest leavel if possible. Thus we define a bool variable that is checked and if true we quit. To achieve this we have to change the reference defined by `&self` to a mutable reference `&mut self`. Otherwise we would not be able to change the value of the bool variable in different functions. 
* Escaoe sequences start with `\x1b` and are followed by 3 other bytes to instruct the terminal what to do. Examples are coloring text, moving the curson or clearing parts of the screen. 
  * In our example `[2J` means clear the screen (=J) 2 = Entrire screen 
  * Documentation of escape sequences https://vt100.net/docs/vt100-ug/chapter3.html

* Importing a file in main.rs is done using the `mod <filename>` syntax, whereas in other files we have to use the `use crate::<filename>::<objectname>` syntax. By importing a file into main even if we dont need it there we reimport the crate an can shorten the import using `crate::<objectname>`.  
* Clearing a screen (using termion or escape sequences), ensures that content that was previously written to the terminal is not shown in the programm (Basically the standard we want with editors)
* With `let welcome_message = format!("Greetings Crustacian. The version of this editor is {}.\r", VERSION);` we can do string interpolation
* Using `&string_name[..some_integers]` we can slice a string until some point. Be careful slicing over the strings length may cause rust to panic
* The datatpye `usize` is using the machines architecture we compile to, to determine the size of an integer
* The pattern `let Position{mut x, mut y} = position` is called **destructuring**. New variables x and y are created and their values are bind to the fields with the same name in position  


### Key Takeaways for Rust

* **Structs** are similiar to python classes and are initiated f.e `pub struct Example {...}`. They have accessor keywords, telling the compiler where the struct should be accessible from. 
  * By using the `impl` keyword we can create an implementation of a struct, allowing us to build more complex class hierachies. 
  * By defining `&self` inside a function from a struct we cann implement methods that run on an instance of a class. On the contrary ommiting `&self`, we can build static functions for a struct
  * Keywords like `pub` or `private` can be used to define the accessibility of a struct or class method
  * `&` can be used to borrow a struct or a value from another function or struct. Borrowing is a **key concept** of Rust, read more here https://doc.rust-lang.org/book/ch04-02-references-and-borrowing.html?highlight=borrow#references-and-borrowing
  * Clippy is a nifty tool to analyse your codebase. Run  `cargo clippy -- -W clippy::pedantic` to get a really strict analysis (including improvements) of your code &Rightarrow; Make sure to run `cargo clean` before clippy, so that your whole code is analyses
  * `match` lets you write pattern matches (like C switch). Using `if let` you can write a shorthand *match* with only 1 case.
  * Rust has **immutability** by default. If a variable needs to be changed (Not reassigend) after creation use `let mut ...`. 
  * Writing a `?` after a function call means, panic if the function returns an error or else continue (A very common idiom in rust)
  * Imporing an file or object to main.rs is done by `mod <filename>`. Using this pattern we can then import object to other modules by `use crate::<filename>::<objectname>`
  * Another important pattern `let Position{mut x, mut y} = position` is called **destructuring**. New variables x and y are created and their values are bind to the fields with the same name in position  