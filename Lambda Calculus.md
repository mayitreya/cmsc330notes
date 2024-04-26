# Lambda Calculus

Lambda Calculus is a language to express the application of functions. At its core, we define anonymous functions, and we can apply those anonymous functions by passing in a value into those functions.

## At its Core

Everything in lambda calculus is an expression $e$ and operations in terms of $e$. For example...
- You can have an expression $e$ just evaluate to a variable $x$, which in most cases represents an argument that you can pass into a function
- You can have $\lambda x.e$, which represents an anonymous function that takes in an argument $x$ and does some operation represented by the $e$. In other words, $\lambda$ represents the declaration of an anonymous function, $x$ is the argument you pass into the anonymous function, and $e$ is the function body. This concept is called an *abstraction* in lambda calculus
- You can have $e \; e$, (notice how there's a space between the two $e$'s) which is simply a function call. The first $e$ is the function, and the second $e$ is the function argument expression. This concept is called *function application* in lambda calculus

## Some Conventions

1. The scope of $\lambda$ extends as far right as possible. What does this mean? Well, it means that when we define a $\lambda$ abstraction, the variable binding introduced by the $\lambda$ operator extends to encompass as much of the expression to the right as it can, unless delimited by parentheses. In other words, the variable declared by the $\lambda$ operator is bound to the maximum possible portion of the expression to its right, ensuring that it applies to the closest expression or function argument without crossing any boundaries set by parentheses

$$\lambda x. \lambda y.x \; y == \lambda x.\left (\lambda y. \left ( x \; y\right ) \right )$$

2. Function application is left-associative

$$x \; y \; z == (x \; y) \; z$$

3. Suppose we had `let x = e1 in e2`. In this case, we're using a `let` binding to give `x` the value of `e1`. In lambda calculus, we have a streamlined way to do the same thing, as such...

$$\left (\lambda x.e_2 \right ) \; e_1$$

As an explanation, let's first look at the OCaml `let` expression above. What are we doing here? We're saying that `x` gets the value `e1` and we take that value of x and apply it somehow onto `e2`. Now, let's look at the stuff inside of the parentheses of the lambda expression. $\lambda x.e_2$ is an anonymous function that takes in an argument $x$, and the body of the anonymous function simply evaluates $e_2$. Now, we're taking that and applying the argument $e_1$, which effectively replaces the $x$ in the lambda expression part.

The whole point of saying this was that the OCaml expression and the lambda expression above are equivalent

## Beta ($\beta$) Reduction
Above, the part where we replaced the $x$ in the lambda expression with $e_1$ shows the basics of beta reduction. To describe it...

$$\left ( \lambda x.e_1 \right ) e_2 \rightarrow \text{Evaluate } e_1 \text{ but use } e_2 \text{ in the place of every } x$$
$$\text{Formally:: } \left ( \lambda x.e_1 \right ) e_2 \rightarrow  e_1 \left [ x:=e_2 \right ]$$

When a term can't be beta reduced further, we say that they're in *beta normal form*

### Example 1
$$\left ( \lambda x.\lambda z.x \; z \right ) \; y$$
The $y$ replaces the first variable... in this case that's the $x$, and the first lambda part goes away. So now we have...

$$\lambda z.y \; z$$

And that's our final answer

### Example 2
$$\left (\lambda x.x \; y \right ) \left (\lambda z.z \; w \right )$$
Same thing here... this is a function application of two lambda expressions, so we replace all instances of the first variable $x$ of the first lambda expression with the entire second expression, so now we have...

$$\left ( \lambda z.z \; w \right ) \; y$$
And we do it again; we take the $y$ and replace the first variable of the lambda expression on the left, $z$. So now we have...

$$y \; w$$
And this is our final reduction

## Terms and Definitions
1. Eager evaluation (Call by Value) (CBV)
2. Lazy Evaluation (Call by Name) (CBN)

First let's talk about CBV. All this means is that before doing beta reduction for the overall expression, you beta reduce the arguments before passing them in. Let's look at some examples...

## CBV

### Example 1
$$\left ( \lambda x.x \; y \right ) \left ( \lambda z.z \right )$$

Here, we substitute all instances of $x$ in the first expression, so we have...

$$\left ( \lambda z.z \right ) \; y$$

This can be beta reduced again. Let's replace all instances of $z$ with $y$. We get...

$$y$$

Now, CBV isn't really shown that well in this example, so let's look at a harder one with some notes

### Example 2
$$\left ( \lambda x. \lambda y.x \; y \right ) \left ( \lambda z.z \; z \right ) \; x$$

It looks like we have an expression $\left ( \lambda x. \lambda y.x \; y \right )$ with two arguments $\left ( \lambda z.z \; z \right )$ and $x$. Let's pass in the first argument first. We get...

$$\left (  \lambda y.\left ( \lambda z.z \; z \right ) \; y \right ) \; x$$

Now, we take the $x$ and replace the $y$ in the left expression. Now we have...

$$\left ( \lambda z.z \; z \right ) \; x$$

Finally, we replace the $z$ with $x$ and we get...

$$x \; x$$

as our final answer

### Example 2 Note
A note that we should consider is parenthesis placement in the example. Initially we had... 

$$\left ( \lambda x. \lambda y.x \; y \right ) \left ( \lambda z.z \; z \right ) \; x$$

But what if we had...

$$\left ( \lambda x. \lambda y.x \; y \right ) \left ( \left ( \lambda z.z \; z \right ) \; x \right )$$

Well, now that the $\lambda z.z \; z$ and the $x$ are grouped as one argument, we must simplify that first, before passing it into $\lambda x.\lambda y.x \; y$

So we have...

$$\left ( \lambda x.\lambda y.x \; y \right ) \; x \; x$$

Now let's pass in the first $x$

$$\left ( \lambda y.x \; y \right ) \; x$$

Now let's pass in the second $x$

$$x \; x$$

## CBN
Also known as lazy evaluation. We simply *don't* simplify any arguments before passing them in, unlike CBV, where you do

## More Terms and Definitions
1. Free variable
2. Bound variable

## Free Variables and Bound Variables
A variable in a lambda expression that is not bound by a lambda abstraction. Remember, a lambda abstraction is defined at the very top

### Example 1
$$\lambda x.x \; y$$

In this example, $y$ is the free variable because only $x$ is bound by the lambda abstraction

### Example 2
$$(λx_1.x_1 \; (λy_1.y_1 \; z_1)) \; (λz_2.z_2 \; x_2)$$

In this example, I added subscripts to each variable, but they mean nothing. I just added them to make some distinctions

1. $x_1$ is a bound variable because there is an $x_1$ abstraction at the beginning
2. $y_1$ is a bound variable because there is a $y_1$ abstraction at the scope where $y_1$ is
3. $z_1$ is a free variable because up until that point, there is no $z_1$ lambda abstraction
4. $z_2$ is a bound variable because there is a $z_2$ abstraction at that scope
5. $x_2$ is a bound variable because all the way at the beginning, there is an $x$ abstraction (the subscripts mean nothing)

## Alpha ($\alpha$) Conversion
1. Quick aside: *Lambda Calculus uses static scoping.* What this means is that the meaning of the variables is determined at the time that they're defined, rather than at runtime
2. Quick aside 2: Consider this expression below...

$$\left ( \lambda x.x \; \left ( \lambda x.x \right )\right ) \; z$$

in this expression, the rightmost $x$ inside the inner lambda expression refers to the second binding of $x$ (shadowing)

3. Quick aside 3: $\left ( \lambda x.x \; \left ( \lambda x.x \right )\right ) \; z$ represents a function that takes its argument and applies it to the identity function
4. Relating to (3), the identity function, represented by $\lambda x.x$, returns its argument unchanged

### Now For Actual Alpha Conversion Stuff
Alpha conversion refers to renaming bound variables in lambda expressions while preserving their meaning. For example...

$$\lambda x.x == \lambda y.y == \lambda z.z$$
$$\lambda y.\lambda x.\lambda y == \lambda z.\lambda x.\lambda z$$

### What is the Point of Alpha Conversion?
The point all comes down to clarity. Let's say for example we have this expression...

$$\left ( \lambda x.\lambda y.x \; y \right ) \; y$$

What if we were to use beta reduction here?

$$\lambda y.y \; y$$

Wait a minute, this is semantically not the same as the original expression! How can we fix this?

Well, we can simply change the inner $y$ to something else so that it doesn't conflict with the argument $y$ that we pass in; for instance...

$$\left ( \lambda x.\lambda y.x \; y \right ) \; y == \left ( \lambda x.\lambda z.x \; z \right ) \; y$$

This is equivalent so far. Now let's reduce the new expression into...

$$\lambda z.y \; z$$

...which is semantically the same as the original lambda expression!

In summary, we *substituted* a new *fresh* variable to avoid semantic errors when doing beta reduction

## Encoding Programming Concepts Using Lambda Calculus
### Let Bindings
`let x = e1 in e2` turns into $\left ( \lambda x.e_2 \right ) \; e_1$

As an example, `let x = (λy.y) in x x` turns into $\left ( \lambda x.x \; x \right ) \left ( \lambda y.y \right )$

### Booleans and Boolean Logic
1. `true` turns into $\lambda x.\lambda y.x$
2. `false` turns into $\lambda x.\lambda y.y$
3. `if BOOL then a else b` turns into $\text{(true or false, defined above) } (a) \; (b)$

As an example, `if true then b else c` turns into $\left ( \lambda x.\lambda y.x \right ) (b) \; (c)$

4. `not (x)` turns into $\left ( \lambda x.x \text{ false true}\right )$. Notice how this is similar to the `if` statement defined above. Let's look at an example...

`not true` turns into `if true then false else true` which turns into $\text{false true false}$ which evaluates to `false`

5. Similarly, `and` can be converted into an `if` statement as well, as such...

`and (x) (y)` turns into `if x then y else false`

6. So can `or`, as such...

`or (x) (y)` turns into `if x then true else y`

## Tuple Pairs
1. `(a, b)` turns into $\lambda x.\left ( \text{if } x \text{ then } a \text{ else } b \right )$
2. `fst` turns into $\lambda f.f \text{ true}$
3. `snd` turns into $\lambda f.f \text{ false}$

As an example, `fst (a, b)` turns into $\left ( \lambda f.f \text{ true} \right ) \left ( \lambda x.\left ( \text{if } x \text{ then } a \text{ else } b \right ) \right )$ which reduces to `if true then a else b` which is of course `a`

## The Rest That You Probably Don't Need To Know But It's Better To Understand That These Exist
1. Integers
2. Successor (which allows for addition, multiplication)
3. is_zero
4. Predecessor (which allows for subtraction, division)
5. Looping and recursion