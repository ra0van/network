### KeyPoints on why Rust ?
- **Speed** - Rust pays no penalty for abstraction & pays for only features used.
- **Safety**
	- Rust **doesn't have garbage collector**
	- Ensures memory safety using **ownership** and **borrowing** concept
	- Significant difference b/w C, C++ & Rust is writing safe code.
	- Objects are managed by Programming language from beginning to end. The proper amount of memory required is allocated automatically & deallocated when no longer in use.
- **Cargo Manager** - System languages (c/c++) do not have a package manger. Rust has cargo manager to manager libraries & frameworks
- **Error Messages** - Rust formats error messages & checks for typos 
- **Efficient C Binding** - Rust can interoperate with C language. It provides foreign function interface to communicate with C APIs. Due to ownership it can guarantee memory safety.
- **Threads without Data Races** - Rust uses ownership to avoid data races. 

Basics of Rust
- Ends with .rs files
- main() function is beginning of every rust program.
```
fn main() {
	println!("Hello World!");
}
```
- semicolon is optional at the end of the last expression only
- `println!` is a macro

>What is a macro?
A macro is an expression which has an exclamation mark before the parenthesis
>
What are macros used for?
They are used in meta-programming i.e code that writes code. They look like functions in other system languages like C/C++, but instead of generating a function call like functions, they are expanded into source code that gets compiled with the rest of the program. Why? In this way they provide more runtime features.
>
>![[RustMacroExplained.png]]
>Rust provides built-in macros. Users can also define their own macros

### Variables
`let` keyword is used for variable declaration. `let` is an **identifier**
>`let language = "Rust";`

> **Naming Convention** By convention, we should write a variable name in snake_case i.e, 
>  - All letters should be lowercase
>  - All words should be separated using an underscore

Rust refers to declarations as bindings as they bind a name at the time of creation. let is kind of declaration statement.

#### Mutable variables
`let mut language = "Rust";

Multiple variable initialisation 
`let (name, place) = ("Rajasekhar", "Bengaluru")`

Rust throws warning for unassigned or unused variables. To remove such warnings write the expression `# [allow(unused_variables, unused_mut)]` at the start of the program code.

#### Scope and Shadowing
- Has local variable & Global variables
- Global variable is declared using `const` keyword
- Local variables are declared within a block of code `{ }`
- **Shadowing** is a technique in which variable declared within a certain scope has the same name as a variable declared in outer scope. This is also know as masking. This outer variable is said to be shadowed by the inner variable, while the inner variable is said to mask the outer variable.

##### Data types
- **Implicit definition** - Rust can infer the type from assigned value
- **Explicit definition** - `let variable name:datatype = value`

![[RustDataTypes|500x300]]

Integers can be fixed size & variable size as well. Fixed size Integers can be signed & unsigned & of different sizes 8-64

```
fn main(){
	let a:i32 = 24;
	let b:u64
}
```