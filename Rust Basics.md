# Rust Basics

## Some Information First
1. Rust is compiled
2. Rust is memory safe and also low level like C and C++
3. The main feature of Rust is the concepts of *lifetimes* and *ownership*
4. The default is that variables and bindings are *immutable*
5. There is type inference
6. There is polymorphism

## The Main Function
This is the `main` function in Rust, with some Hello World print statements inside
```rust
// This is a comment in Rust
fn main() {
    println!( "Hello World!" );
    print!("Hello world but without a newline");
}
```
Sidenote: The reason why the `print` function has an exclamation mark is because it's a macro

## Let Bindings
### Example 1
If we say
```rust
{
    let x = 37;
    let y = x + 5;
    x
}
```
Notice a few things
1. This is a valid progam and returns 42
2. Scope, just like C and Java, is defined in curly braces
3. Every statement has a semicolon at the end of it, but the return value `x` does NOT have a semicolon at the end of it
4. `let` bindings in Rust are the same as the ones in OCaml

### Example 2
If we say
```rust
{
    let x = 37;
    let x = x + 5;
    x
}
```
Notice a few things
1. This is a valid program and returns 42
2. We have shadowed `x` and given it a new value
3. We should avoid shadowing as it causes confusion

### Example 3
If we say
```rust
{
    let x = 37;
    x = x + 5;
    x
}
```
Notice that
1. This is NOT a valid program! Variables are immutable by default, which means that you cannot change the value of `x` in this context. Instead we can do this...

```rust
{
    let mut x = 37;
    x = x + 5;
    x
}
```
Now, notice that
1. `x` is defined explicitly as mutable (using the `mut` keyword)
2. Therefore this is a valid program

### Example 4
If we say
```rust
{
    let x:u32 = -1;
    let y = x + 5;
    y
}
```
Notice that
1. This is NOT a valid program because we're forcing `x` to be a `u32` (meaning unsigned 32-bit integer) but we're giving `x` the value of `-1` which is a signed integer. Instead we can do this...

```rust
{
    let x:i16 = -1;
    let y:i16 = x + 5;
    y
}
```
Notice a few things
1. We've forced both `x` and `y` to be unsigned 16-bit integers (they should be consistent!)
2. As stated in a previous section, we've said that types are inferred by default, which means that the `i16` wasn't even necessary

## Conditionals
If statements in Rust look like this...
```rust
fn main() {
    let n = 5;
    if n < 0 {
        print!( "{} is negative", n );
    }
    else if n > 0 {
        print!( "{} is positive", n );
    }
    else {
        print!( "{} is zero", n );
    }
}
```
Basically the same as in Java and C

Conditionals are expressions like they are in OCaml, which means that you're allowed to bind conditionals to variables, as such...

```rust
fn main() {
    let n = 5;
    let x = if n < 0 {
        10
    }
    else if n > 0 {
        "a"
    };

    print!( "{:?}|", x );
}
```
Notice a few things
1. This is NOT a valid program because the return types for the conditional are not the same
2. The return values in the conditional themselves don't have semicolons, because they're return values, and those don't have semicolons
3. The `:?` in the `print` statement signifies that you're "debug" printing. This means that even though there's no defined way to print the value of a conditional, by using the debug print flag, you're telling Rust to just suck it up and print it anyways

### The Obligatory Factorial Example
```rust
fn fact( n:i32 ) -> i32 {
    if n == 0 {
        1
    }
    else {
        let x = fact( n - 1 );
        n * x
    }
}

fn main() {
    let res = fact( 6 );
    println!( "fact( 6 ) = {}", res );
}
```
Notice a couple things
1. `fact` takes in a parameter `n` which is forced to be a signed 32-bit integer, signified by the `n:i32`
2. `fact` is forced to return a signed 32-bit integer, signified by the arrow pointing to `i32`

## Types of Loops
### Infinite loop that you break out of
```rust
fn fact( n: u32 ) -> u32 {
    let mut x = n;
    let mut a = 1;

    loop {
        if x <= 1 {
            break;
        }
        a = a * x;
        x = x - 1;
    }

    a
}
```
Notice the `loop` section of the example above is the infinite loop that gets broken out of

### While loops
```rust
while condition_satisfied {
    do_this
}
```

### For loops
1. In Rust, `(a..b)` generates all values starting (and including) `a`, all the way up to (and NOT including) `b`
2. With that being said, something like `(1..5)` would return the range 1-4
```rust
{
    for number in (0..10) {
        println!( "{}", number );
    }
}
```
This will print the following...
```
0
1
2
3
4
5
6
7
8
9
```

Just like `if` statements, loops are also expressions which can be bound to variables
- Note that loops return the final value if there is one. If there isn't, the loop returns a unit like the one in OCaml

## A Quirk About `break`
You can return values as you're `break`ing. What does this mean? Well, let's look at an example

```rust
fn main() {
    let mut counter = 0;

    let result = loop {
        counter += 1;

        if counter == 10 {
            break counter * 2;
        }
    };

    println!( "The result is {result}" );
}
```
Observe that in this example, we're looping until `counter` hits 10. Once it does, we `break`, and as we do that, we return `counter * 2` which will always be `20`

### Another Example
```rust
{    
    let mut x = 5;
    let y = loop {
        x += x - 3;
        println!("{}", x);
        x % 5 == 0 { break x; }
    };

    print!("{}",y);
}
```

## n-Tuples in Rust
- Same as the ones in OCaml
- They can have multiple types inside them

### Example 1
```rust
{
    let tuple = ( "hello",  5, 'c' );
    assert_eq!( tuple.0, "hello" );
    let ( x, y, z ) = tuple;
}
```
Observe that
1. The tuple is of length three, and has three different types inside of it
2. The `assert_eq!` macro asserts if two things are equal. In this case, we're accessing the first element of the tuple by using the dot `(.)` operator, and checking to see if it's equal to the string `"hello"`.
3. We are also binding `tuple` to a new tuple with elements `x`, `y`, and `z`

### Example 2
```rust
fn dist( s:( f64,f64 ), e:( f64,f64 ) ) -> f64 {
    let ( sx,sy ) = s;
    let ex        = e.0;
    let ey        = e.1;
    let dx        = ex - sx;
    let dy        = ey - sy;

    ( dx*dx + dy*dy ).sqrt() // Return value
}
```
Observe that
1. We're forcing the parameters `s` and `e` to be length-2-tuples, where both elements of the tuple are 64-bit floats
2. We're forcing the function to return a 64-bit float, denoted by the arrow

## Arrays in Rust
1. Arrays can be mutable or immutable, up to you! $-$ but they MUST be of fixed length
2. You can index an array the same way as you can in Java or C using subscripting
3. You can assign variables array indices
4. Arrays must be homogenous!

### Example
```rust
let nums   = [1,2,3]; // Type is [i32; 3]
let strs   = ["Monday", "Tuesday", "Wednesday"]; // [&str;3]
let x      = nums[0]; // 1
let s      = strs[1]; // "Tuesday"
let mut xs = [1,2,3];
xs[0]      = 1; // Ok, since xs mutable
let i      = 4;
let y      = nums[i]; // Fails (panics) at run-time
```
Notice that on the first line, nums has the type `[i32; 3]` where `i32` is the type of each element, and `3` is the length of the array. *ALL ARRAYS HAVE THESE TWO PROPERTIES!*

### Iterating Through Arrays
Using a for loop!
```rust
{
    let arr = [1, 2, 3, 4];
    for element in arr.iter() {
        println!( "{}", element );
    }
}
```
The output is...
```
1
2
3
4
```
- Note: It's impossible for you to go out of bounds for an array (Rust is memory safe!)

## Ownership and Lifetimes in Rust (New Concept)
- Rust does not have a garbage collector. Instead, ownership is employed to get rid of old unused variables
- *All data has a single owner!* Immutable aliases are okay, but mutation of a variable can only happen if you reference the original variable, OR you borrow from the original variable *ONCE*
- The *lifetime* of a variable determines how long a variable can be referenced
- In C and C++ when a new value is allocated to the heap, you manually call `free()` when you're done with that value
- In Java, the garbage collector takes care of the unused value
- In Rust, there is no garbage collector, or no manual `free()` call. Instead, once the owner of a variable is out of scope, it gets automatically `free`d

### Rules of Rust Ownership
1. Each variable has an owner
2. There can only be one owner at a time for any given variable
3. When the variable is out of scope, the variable will be `free`d automatically (dropped)

### Transferring Ownership
```rust
// x is the owner, so we can do any operations on x
let x = String::from( "hello" );
```
Notice that we're assigning `x` to a `String`
```rust
let y = x;
// We've just moved ownership from x to y, so now all operations have to be on y
```
```rust
println!( "{}", y ); // Will work after the above two statements
println!( "{}", x ); // Will FAIL because x is not the owner!
```
The reason that a variable only has one owner is to prevent double `free()` calls

### A Counterexample To Transferring Ownership
```rust
let x = 5;
let y = x;

println!( "{}", y ); // This is good
println!( "{}", x ); // This is also good
```
Why is the second thing good? We just said that a variable can only have one owner!

Great question! This does NOT apply to primitive data types because they live on the stack and things on the stack don't need to be `free`d at all. Things like `String`s live in the heap and therefore need to be `free`d

More formally, this is due to the *Copy trait* that primitives have that reference types don't have

A *trait* is a property that a type has. Types with the *Copy* trait do NOT transfer ownership upon assignment. Instead, the new assignment copies all the data that it's aliasing

## Yo, who made the UMD `clone()` machine??
You can explicitly `clone()` an object, which avoids the loss of variable ownership, but obviously this will take more heap space!

### Example
```rust
let x = String::from( "Hello" );
let y = x.clone();

println!( "{}", y ); // This works
println!( "{}", x ); // This works too!
```

## More Ownership Transferring Stuff
We can transfer variable ownership within a different function, as such...
```rust
fn main() {
    let s1 = String::from( "hello" );
    let s2 = id( s1 );
    println!( "{}", s2 );
    println!( "{}", s1 ); // DOES NOT WORK
}
fn id( s:String ) -> String {
    s // s moved to caller, on return
}
```
Observe a few things
1. `s1` is assigned to a String
2. `s2` calls the `id` function with `s1` as its argument
3. Let's jump to the `id` function and see what's happening... well, the function just returns its parameter as is
4. May as well replace `id( s1 )` with just `s1`, so now ownership has been transferred to `s2` from `s1`
5. Printing `s1` will FAIL!

## Borrowing
We can make a temporary reference to a variable owner which will last until the borrower is used somewhere, in which case the reference will die. Terrible wording, let's look at an example below...

### Example
```rust
fn main() {
    let s1 = String::from( "hello!" );
    let len = calc_len( &s1 ); // Here is where we borrow s1
    println!( "the length of '{}' is {}", s1 ,len ); // We can use both s1 AND len with no issues
}
// We specify that we're passing in a temporary reference! Observe the &String
fn calc_len( s: &String ) -> usize {
    s.len() // s dropped; but not its referent
}
```

## Rules of References
1. You can have *one* mutable reference of a variable *OR*
2. Infinite immutable references of a variable
3. References must always be valid (not pointing to dropped or `free`d values)
4. When making an immutable reference to a mutable variable, you may NOT mutate the original variable until the immutable reference is dropped!
5. To permit mutation via reference, use `&mut` instead of just `&`
6. You cannot make a mutable reference of an immutable variable
7. When you make your *one* mutable reference to a variable, the use of the original variable is blocked until the reference is being done used
8. You *cannot* make a mutable reference if immutable references are already in use

## The asterisk (`*`) operator
- The same as the one in C

### Example
```rust
{
    let mut x = 2;
    let mut y = 3;
    let mut r = &mut x;
    *r        = 4;
    r         = &mut y;
    *r        = 5;
}
```

## Lifetimes
```rust
// TODO
```