---
title: "Types & Functions"
files: []
layout: 2-panels-tree

---
This is the second module of the O'Reilly Haskell book by Bryan O'Sullivan, Don Stewart, and John Goerzen.


---
title: Why care about types?
files: []

---
Every expression and function in Haskell has a *type*. For example, the value `True` has the type `Bool`, while the value "foo" has the type `String`. The type of a value indicates that it shares certain properties with other values of the same type. For example, we can add numbers, and we can concatenate lists; these are properties of those types. We say an expression “has type X”, or “is of type X”.

Before we launch into a deeper discussion of Haskell's type system, let's talk about why we should care about types at all: what are they even for? At the lowest level, a computer is concerned with bytes, with barely any additional structure. What a type system gives us is *abstraction*. A type adds meaning to plain bytes: it lets us say “these bytes are text”, “those bytes are an airline reservation”, and so on. Usually, a type system goes beyond this to prevent us from accidentally mixing types up: for example, a type system usually won't let us treat a hotel reservation as a car rental receipt.

The benefit of introducing abstraction is that it lets us forget or ignore low-level details. If I know that a value in my program is a string, I don't have to know the intimate details of how strings are implemented: I can just assume that my string is going to behave like all the other strings I've worked with.

What makes type systems interesting is that they're not all equal. In fact, different type systems are often not even concerned with the same kinds of problems. A programming language's type system deeply colours the way we think, and write code, in that language.

Haskell's type system allows us to think at a very abstract level: it permits us to write concise, powerful programs.


---
title: "Haskell's type system"
files: []

---
There are three interesting aspects to types in Haskell: they are *strong*, they are *static*, and they can be automatically *inferred*. 

Let's talk in more detail about each of these ideas. When possible, we'll present similarities between concepts from Haskell's type system and related ideas in other languages. We'll also touch on the respective strengths and weaknesses of each of these properties.
---
title: Strong Types
files: []

---
When we say that Haskell has a *strong* type system, we mean that the type system guarantees that a program cannot contain certain kinds of errors. These errors come from trying to write expressions that don't make sense, such as using an integer as a function. For instance, if a function expects to work with integers, and we pass it a string, a Haskell compiler will reject this.

We call an expression that obeys a language's type rules *well typed*. An expression that disobeys the type rules is *ill* typed, and will cause a *type error*.

Another aspect of Haskell's view of strong typing is that it will not automatically coerce values from one type to another. (Coercion is also known as casting or conversion.) For example, a C compiler will automatically and silently coerce a value of type `int` into a `float` on our behalf if a function expects a parameter of type `float`, but a Haskell compiler will raise a compilation error in a similar situation. We must explicitly coerce types by applying coercion functions.

Strong typing does occasionally make it more difficult to write certain kinds of code. For example, a classic way to write low-level code in the C language is to be given a byte array, and cast it to treat the bytes as if they're really a complicated data structure. This is very efficient, since it doesn't require us to copy the bytes around. Haskell's type system does not allow this sort of coercion. In order to get the same structured view of the data, we would need to do some copying, which would cost a little in performance.

**The huge benefit of strong typing is that it catches real bugs in our code before they can cause problems.** For example, in a strongly typed language, we can't accidentally use a string where an integer is expected.

>Weaker and stronger types
It is useful to be aware that many language communities have their own definitions of a “strong type”. Nevertheless, we will speak briefly and in broad terms about the notion of strength in type systems.

In academic computer science, the meanings of “strong” and “weak” have a narrowly technical meaning: strength refers to how permissive a type system is. A weaker type system treats more expressions as valid than a stronger type system.

For example, in Perl, the expression `"foo" + 2` evaluates to the number 2, but the expression `"13foo" + 2` evaluates to the number 15. Haskell rejects both expressions as invalid, because the `(+)` operator requires both of its operands to be numeric. Because Perl's type system is more permissive than Haskell's, we say that it is weaker under this narrow technical interpretation.

The fireworks around type systems have their roots in ordinary English, where people attach notions of *value* to the words “weak” and “strong”: we usually think of strength as better than weakness. Many more programmers speak plain English than academic jargon, and quite often academics *really* are throwing brickbats at whatever type system doesn't suit their fancy. The result is often that popular Internet pastime, a flame war.
---
title: Static types
files: []

---
Having a *static* type system means that the compiler knows the type of every value and expression at compile time, before any code is executed. A Haskell compiler or interpreter will detect when we try to use expressions whose types don't match, and reject our code with an error message before we run it.

```haskell
ghci> True && "false"

<interactive>:ool’ with actual type ‘[Ch2:9:ar]’
    Couldn't match expected type ‘B In the second argument of ‘(&&)’, namely ‘"false"’   
    In the expression: True && "false"
    In an equation for ‘it’: it = True && "false"
```

This error message is of a kind we've seen before. The compiler has inferred that the type of the expression `"false"` is `[Char]`. The `(&&)` operator requires each of its operands to be of type `Bool`, and its left operand indeed has this type. Since the actual type of `"false"` does not match the required type, the compiler rejects this expression as ill typed.

Static typing can occasionally make it difficult to write some useful kinds of code. In languages like Python, “duck typing” is common, where an object acts enough like another to be used as a substitute for it. Fortunately, Haskell's system of *typeclasses*, which we will cover in Module 6, Using Typeclasses, provides almost all of the benefits of dynamic typing, in a safe and convenient form. Haskell has some support for programming with truly dynamic types, though it is not quite as easy as in a language that wholeheartedly embraces the notion.

Haskell's combination of strong and static typing makes it impossible for type errors to occur at runtime. While this means that we need to do a little more thinking “up front”, it also eliminates many simple errors that can otherwise be devilishly hard to find. It's a truism within the Haskell community that once code compiles, it's more likely to work correctly than in other languages. (Perhaps a more realistic way of putting this is that Haskell code often has fewer trivial bugs.) 

Programs written in dynamically typed languages require large suites of tests to give some assurance that simple type errors cannot occur. Test suites cannot offer complete coverage: some common tasks, such as refactoring a program to make it more modular, can introduce new type errors that a test suite may not expose.

In Haskell, the compiler proves the absence of type errors for us: a Haskell program that compiles will not suffer from type errors when it runs. Refactoring is usually a matter of moving code around, then recompiling and tidying up a few times until the compiler gives us the “all clear”.

A helpful analogy to understand the value of static typing is to look at it as putting pieces into a jigsaw puzzle. In Haskell, if a piece has the wrong shape, it simply won't fit. In a dynamically typed language, all the pieces are 1x1 squares and always fit, so you have to constantly examine the resulting picture and check (through testing) whether it's correct.


---
title: Type inference
files: []

---
Finally, a Haskell compiler can automatically deduce the types of almost all expressions in a program. This process is known as type inference. Haskell allows us to explicitly declare the type of any value, but the presence of type inference means that this is almost always optional, not something we are required to do. 
---
title: What to expect from the type system
files: []

---
Our exploration of the major capabilities and benefits of Haskell's type system will span a number of chapters. Early on, you may find Haskell's types to be a chore to deal with.

For example, instead of simply writing some code and running it to see if it works as you might expect in Python or Ruby, you'll first need to make sure that your program passes the scrutiny of the type checker. Why stick with the learning curve? 

While strong, static typing makes Haskell safe, type inference makes it concise. The result is potent: we end up with a language that's both safer than popular statically typed languages, and often more expressive than dynamically typed languages. This is a strong claim to make, and we will back it up with evidence throughout the book.

Fixing type errors may initially feel like more work than if you were using a dynamic language. It might help to look at this as moving much of your debugging *up front*. The compiler shows you many of the logical flaws in your code, instead of leaving you to stumble across problems at runtime.

Furthermore, because Haskell can infer the types of your expressions and functions, you gain the benefits of static typing *without* the added burden of “finger typing” imposed by less powerful statically typed languages. In other languages, the type system serves the needs of the compiler. In Haskell, it serves you. The tradeoff is that you have to learn to work within the framework it provides.

We will introduce new uses of Haskell's types throughout this book, to help us to write and test practical code. As a result, the complete picture of why the type system is worthwhile will emerge gradually. While each step should justify itself, the whole will end up greater than the sum of its parts.
---
title: Some common basic types
files: []

---
In the section called “First steps with types”, we introduced a few types. Here are several more of the most common base types. 5 comments

- A `Char` value represents a Unicode character.
- A `Bool` value represents a value in Boolean logic. The possible values of type `Bool` are `True` and `False`.
- The `Int` type is used for signed, fixed-width integer values. The exact range of values representable as Int depends on the system's longest “native” integer: on a 32-bit machine, an `Int` is usually 32 bits wide, while on a 64-bit machine, it is usually 64 bits wide. The Haskell standard only guarantees that an `Int` is wider than 28 bits. (There exist numeric types that are exactly 8, 16, and so on bits wide, in signed and unsigned flavours; we'll get to those later.)
- An `Integer` value is a signed integer of unbounded size. Integers are not used as often as `Ints`, because they are more expensive both in performance and space consumption. On the other hand, `Integer` computations do not silently overflow, so they give more reliably correct answers
- Values of type `Double` are used for floating point numbers. A `Double` value is typically 64 bits wide, and uses the system's native floating point representation. (A narrower type, `Float`, also exists, but its use is discouraged; Haskell compiler writers concentrate more on making `Double` efficient, so `Float` is much slower.)

We have already briefly seen Haskell's notation for types in the section called “First steps with types”. When we write a type explicitly, we use the notation `expression :: MyType` to say that expression has the type `MyType`. If we omit the `::` and the type that follows, a Haskell compiler will infer the type of the expression.

```haskell
ghci> :type 'a'
'a' :: Char
ghci> 'a' :: Char
'a'
ghci> [1,2,3] :: Int

<interactive>:1:0:
    Couldn't match expected type `Int' against inferred type `[a]'
    In the expression: [1, 2, 3] :: Int
    In the definition of `it': it = [1, 2, 3] :: Int
```

The combination of `::` and the type after it is called a type signature.

---
title: Function application
files: []

---
Now that we've had our fill of data types for a while, let's turn our attention to working with some of the types we've seen, using functions.

To apply a function in Haskell, we write the name of the function followed by its arguments.

ghci> odd 3
True
ghci> odd 6
False

We don't use parentheses or commas to group or separate the arguments to a function; merely writing the name of the function, followed by each argument in turn, is enough. As an example, let's apply the `compare` function, which takes two arguments.

```haskell
ghci> compare 2 3
LT
ghci> compare 3 3
EQ
ghci> compare 3 2
GT
```

If you're used to function call syntax in other languages, this notation can take a little getting used to, but it's simple and uniform.

Function application has higher precedence than using operators, so the following two expressions have the same meaning.

```haskell
ghci> (compare 2 3) == LT
True
ghci> compare 2 3 == LT
True
```

The above parentheses don't do any harm, but they add some visual noise. Sometimes, however, we must use parentheses to indicate how we want a complicated expression to be parsed.

```haskell
ghci> compare (sqrt 3) (sqrt 6)
LT
```

This applies `compare` to the results of applying `sqrt 3` and `sqrt 6`, respectively. If we omit the parentheses, it looks like we are trying to pass four arguments to compare, instead of the two it accepts.


---
title: Lists and Tuples
files: []

---
A *composite* data type is constructed from other types. The most common composite data types in Haskell are lists and tuples.

We've already seen the list type mentioned in the section called “Strings and characters”, where we found that Haskell represents a text string as a list of `Char` values, and that the type “list of Char” is written `[Char]`.

The `head` function returns the first element of a list.

```haskell
ghci> head [1,2,3,4]
1
ghci> head ['a','b','c']
'a'
```

Its counterpart, `tail`, returns all but the `head` of a list.

```haskell
ghci> tail [1,2,3,4]
[2,3,4]
ghci> tail [2,3,4]
[3,4]
ghci> tail [True,False]
[False]
ghci> tail "list"
"ist"
ghci> tail []
*** Exception: Prelude.tail: empty list
```

As you can see, we can apply `head` and `tail` to lists of different types. Applying head to a `[Char]` value returns a `Char` value, while applying it to a `[Bool]` value returns a `Bool` value. The `head` function doesn't care what type of list it deals with.

Because the values in a list can have any type, we call the list type *polymorphic*. When we want to write a polymorphic type, we use a type *variable*, which must begin with a lowercase letter. A type variable is a placeholder, where eventually we'll substitute a real type.

We can write the type `“list of a”` by enclosing the type variable in square brackets: `[a]`. This amounts to saying “I don't care what type I have; I can make a list with it”.

>#Distinguishing type names and type variables
We can now see why a type name must start with an uppercase letter: this makes it distinct from a type variable, which must start with a lowercase letter.



When we talk about a list with values of a specific type, we substitute that type for our type variable. So, for example, the type `[Int]` is a list of values of type `Int`, because we substituted `Int` for `a`. Similarly, the type `[MyPersonalType]` is a list of values of type `MyPersonalType`. We can perform this substitution recursively, too: `[[Int]]` is a list of values of type `[Int]`, i.e. a list of lists of `Int`.

```haskell
ghci> :type [[True],[False,False]]
[[True],[False,False]] :: [[Bool]]
```

The type of this expression is a list of lists of `Bool`.

>#Lists are special
Lists are the “bread and butter” of Haskell collections. In an imperative language, we might perform a task many items by iterating through a loop. This is something that we often do in Haskell by traversing a list, either by recursing or using a function that recurses for us. Lists are the easiest stepping stone into the idea that we can use data to structure our program and its control flow. We'll be spending a lot more time discussing lists in Module 4, Functional programming.

A tuple is a fixed-size collection of values, where each value can have a different type. This distinguishes them from a list, which can have any length, but whose elements must all have the same type.

To help to understand the difference, let's say we want to track two pieces of information about a book. It has a year of publication, which is a number, and a title, which is a string. We can't keep both of these pieces of information in a list, because they have different types. Instead, we use a tuple.

```haskell
ghci> (1964, "Labyrinths")
(1964,"Labyrinths")
```
We write a tuple by enclosing its elements in parentheses and separating them with commas. We use the same notation for writing its type.

```haskell
ghci> :type (True, "hello")
(True, "hello") :: (Bool, [Char])
ghci> (4, ['a', 'm'], (16, True))
(4,"am",(16,True))
```

There's a special type, `()`, that acts as a tuple of zero elements. This type has only one value, also written `()`. Both the type and the value are usually pronounced “unit”. If you are familiar with C, `()` is somewhat similar to `void`.

Haskell doesn't have a notion of a one-element tuple. Tuples are often referred to using the number of elements as a prefix. A 2-tuple has two elements, and is usually called a *pair*. A “3-tuple” (sometimes called a *triple*) has three elements; a 5-tuple has five; and so on. In practice, working with tuples that contain more than a handful of elements makes code unwieldy, so tuples of more than a few elements are rarely used.

A tuple's type represents the number, positions, and types of its elements. This means that tuples containing different numbers or types of elements have distinct types, as do tuples whose types appear in different orders.

```haskell
ghci> :type (False, 'a')
(False, 'a') :: (Bool, Char)
ghci> :type ('a', False)
('a', False) :: (Char, Bool)
```

In this example, the expression `(False, 'a')` has the type `(Bool, Char)`, which is distinct from the type of `('a', False)`. Even though the number of elements and their types are the same, these two types are distinct because the positions of the element types are different. 

```haskell
ghci> :type (False, 'a', 'b')
(False, 'a', 'b') :: (Bool, Char, Char)
```

This type, `(Bool, Char, Char)`, is distinct from `(Bool, Char)` because it contains three elements, not two.

We often use tuples to return multiple values from a function. We can also use them any time we need a fixed-size collection of values, if the circumstances don't require a custom container type.


---
title: Excercises
files: []

---
What are the types of the following expressions?

- `False`
- `(["foo", "bar"], 'a')`
- `[(True, []), (False, [['a']])]`


---
title: Functions over lists and tuples
files: []

---
Our discussion of lists and tuples mentioned how we can construct them, but little about how we do anything with them afterwards. We have only been introduced to two list functions so far, `head` and `tail`.

A related pair of list functions, `take` and `drop`, take two arguments. Given a number `n` and a list, 

- `take` returns the first n elements of the list
- `drop` returns all but the first n elements of the list. 

As these functions take two arguments, notice that we separate each function and its arguments using white space.

```haskell
ghci> take 2 [1,2,3,4,5]
[1,2]
ghci> drop 3 [1,2,3,4,5]
[4,5]
```

For tuples, the `fst` and `snd` functions return the first and second element of a pair, respectively.

```haskell
ghci> fst (1,'a')
1
ghci> snd (1,'a')
'a'
```

If your background is in any of a number of other languages, each of these may look like an application of a function to two arguments. Under Haskell's convention for function application, each one is an application of a function to a single pair.

>#Haskell tuples aren't immutable lists
If you are coming from the Python world, you'll probably be used to lists and tuples being almost interchangeable. Although the elements of a Python tuple are immutable, it can be indexed and iterated over using the same methods as a list. This isn't the case in Haskell, so don't try to carry that idea with you into unfamiliar linguistic territory.

As an illustration, take a look at the type signatures of `fst` and `snd`: they're defined only for pairs, and can't be used with tuples of other sizes. Haskell's type system makes it tricky to write a generalised “get the second element from any tuple, no matter how wide” function.


---
title: Passing an expression to a function
files: []

---
In Haskell, function application is left associative. This is best illustrated by example: the expression `a b c d` is equivalent to `(((a b) c) d)`. If we want to use one expression as an argument to another, we have to use explicit parentheses to tell the parser what we really mean. Here's an example.

```haskell
ghci> head (drop 4 "azerty")
't'
```

We can read this as “pass the expression `drop 4 "azerty"` as the argument to `head`”. If we were to leave out the parentheses, the offending expression would be similar to passing three arguments to `head`. Compilation would fail with a type error, as head requires a single argument, a list.


---
title: Function types and purity
files: []

---
Let's take a look at a function's type.

```haskell
ghci> :type lines
lines :: String -> [String]
```

We can read the `->` above as “to”, which loosely translates to “returns”. The signature as a whole thus reads as “lines has the type `String` to list-of-`String`”. Let's try applying the function.

```haskell
ghci> lines "the quick\nbrown fox\njumps"
["the quick","brown fox","jumps"]
```

The lines function splits a string on line boundaries. Notice that its type signature gave us a hint as to what the function might actually do: it takes one `String`, and returns many. This is an incredibly valuable property of types in a functional language.

A side effect introduces a dependency between the global state of the system and the behaviour of a function. For example, let's step away from Haskell for a moment and think about an imperative programming language. Consider a function that reads and returns the value of a global variable. If some other code can modify that global variable, then the result of a particular application of our function depends on the current value of the global variable. The function has a side effect, even though it never modifies the variable itself.

Side effects are essentially invisible inputs to, or outputs from, functions. In Haskell, the default is for functions to *not* have side effects: the result of a function depends only on the inputs that we explicitly provide. We call these functions *pure*; functions with side effects are *impure*.

If a function has side effects, we can tell by reading its type signature: the type of the function's result will begin with IO.

```haskell
ghci> :type readFile
readFile :: FilePath -> IO String
```

Haskell's type system prevents us from accidentally mixing pure and impure code.
---
title: "Haskell source files, and writing simple functions"
files:
  - action: open
    path: add.hs
    panel: 0
  - action: open
    path: "#terminal: ghci"
    panel: 0
layout: ""

---
Now that we know how to apply functions, it's time we turned our attention to writing them. While we can write functions in ghci, it's not a good environment for this. It only accepts a highly restricted subset of Haskell: most importantly, the syntax it uses for defining functions is not the same as we use in a Haskell source file[5]. Instead, we'll finally break down and create a source file.

Haskell source files are usually identified with a suffix of `.hs`.

>#Get the source code
If you look in the file tree, you should now see a file `add.hs` with code already there. Feel free to open it up, although it should already have opened up for you, along with a terminal window.

On the left hand side of the `=` is the name of the function, followed by the arguments to the function. On the right hand side is the body of the function. With our source file saved, we can load it into ghci, and use our new add function straight away. (The prompt that ghci displays will change after you load your file.) 

```haskell
ghci> :load add.hs
[1 of 1] Compiling Main             ( add.hs, interpreted )
Ok, modules loaded: Main.
ghci> add 1 2
3
```

>#What if ghci cannot find your source file?
When you run ghci it may not be able to find your source file. It will search for source files in whatever directory it was run. If this is not the directory that your source file is actually in, you can use ghci's `:cd` command to change its working directory.

```haskell
ghci> :cd /tmp
```

Alternatively, you can provide the path to your Haskell source file as the argument to :load. This path can be either absolute or relative to ghci's current directory.

When we apply `add` to the values `1` and `2`, the variables `a` and `b` on the left hand side of our definition are given (or “bound to”) the values `1` and `2`, so the result is the expression `1 + 2`.

Haskell doesn't have a `return` keyword, as a function is a single expression, not a sequence of statements. The value of the expression is the result of the function. (Haskell does have a function called `return`, but we won't discuss it for a while; it has a different meaning than in imperative languages.)

When you see an `=` symbol in Haskell code, it represents “meaning”: the name on the left is defined to be the expression on the right.

---
title: "Just what is a variable, anyway?"
files:
  - action: close
    path: "#tabs"
  - action: open
    path: Assign.hs
    panel: 0
  - action: open
    path: "#terminal: ghci"
    panel: 0
layout: ""

---
In Haskell, a variable provides a way to give a name to an expression. Once a variable is *bound* to (i.e. associated with) a particular expression, its value does not change: we can always use the name of the variable instead of writing out the expression, and get the same result either way.

If you're used to imperative programming languages, you're likely to think of a variable as a way of identifying a memory location (or some equivalent) that can hold different values at different times. In an imperative language we can change a variable's value at any time, so that examining the memory location repeatedly can potentially give different results each time.

The critical difference between these two notions of a variable is that in Haskell, once we've bound a variable to an expression, we know that we can always substitute it for that expression, because it will not change. In an imperative language, this notion of substitutability does not hold.

For example, if we run the following tiny Python script, it will print the number 11.

```python
x = 10
x = 11
# value of x is now 11
print x
```

In contrast, trying the equivalent in Haskell results in an error. 3 comments

```haskell
-- file: ch02/Assign.hs
x = 10
x = 11
```

We cannot assign a value to x twice.

```haskell
ghci> :load Assign
[1 of 1] Compiling Main             ( Assign.hs, interpreted )

Assign.hs:4:0:
    Multiple declarations of `Main.x'
    Declared at: Assign.hs:3:0
                 Assign.hs:4:0
Failed, modules loaded: none.
```

---
title: Conditional evaluation
files:
  - action: close
    path: "#tabs"
  - action: open
    path: myDrop.hs
    panel: 0
    index: 0
    type: file
    arg: myDrop.hs
  - action: open
    path: "#terminal: ghci"
    panel: 0
    index: 1
    type: terminal
    arg: ghci
layout: ""

---
Like many other languages, Haskell has an `if` expression. Let's see it in action, then we'll explain what's going on. As an example, we'll write our own version of the standard `drop` function. Before we begin, let's probe a little into how `drop` behaves, so we can replicate its behaviour.

```haskell
ghci> drop 2 "foobar"
"obar"
ghci> drop 4 "foobar"
"ar"
ghci> drop 4 [1,2]
[]
ghci> drop 0 [1,2]
[1,2]
ghci> drop 7 []
[]
ghci> drop (-2) "foo"
"foo"
```
From the above, it seems that drop returns the original list if the number to remove is less than or equal to zero. Otherwise, it removes elements until either it runs out or reaches the given number. Here's a myDrop function that has the same behaviour, and uses Haskell's if expression to decide what to do. The null function below checks whether a list is empty.

>Source Code
>You should now have a terminal window and `myDrop.hs` opened up in the left panel. Take a look at the `myDrop.hs` function.

In Haskell, **indentation is important**: it continues an existing definition, instead of starting a new one. Don't omit the indentation!

You might wonder where the variable name `xs` comes from in the Haskell function. This is a common naming pattern for lists: you can read the `s` as a suffix, so the name is essentially “plural of x”.

```haskell
ghci> :load myDrop.hs
[1 of 1] Compiling Main             ( myDrop.hs, interpreted )
Ok, modules loaded: Main.
ghci> myDrop 2 "foobar"
"obar"
ghci> myDrop 4 "foobar"
"ar"
ghci> myDrop 4 [1,2]
[]
ghci> myDrop 0 [1,2]
[1,2]
ghci> myDrop 7 []
[]
ghci> myDrop (-2) "foo"
"foo"
```

Now that we've seen `myDrop` in action, let's return to the source code and look at all the novelties we've introduced.

First of all, we have introduced `--`, the beginning of a single-line comment. This comment extends to the end of the line.

Next is the `if` keyword itself. It introduces an expression that has three components.

- An expression of type `Bool`, immediately following the `if`. We refer to this as a *predicate*.
- A `then` keyword, followed by another expression. This expression will be used as the value of the `if` expression if the predicate evaluates to `True`.
- An `else` keyword, followed by another expression. This expression will be used as the value of the `if` expression if the predicate evaluates to `False`.

We'll refer to the expressions after the `then` and `else` keywords as “branches”. The branches must have the same types; the `if` expression will also have this type. An expression such as `if True then 1 else "foo"` has different types for its branches, so it is ill typed and will be rejected by a compiler or interpreter.

Recall that Haskell is an expression-oriented language. In an imperative language, it can make sense to omit the else branch from an if, because we're working with *statements*, not expressions. However, when we're working with expressions, an `if` that was missing an else wouldn't have a result or type if the predicate evaluated to `False`, so it would be nonsensical.

Our predicate contains a few more novelties. The null function indicates whether a list is empty, while the `(||)` operator performs a logical “or” of its Bool-typed arguments.

```haskell
ghci> :type null
null :: [a] -> Bool
ghci> :type (||)
(||) :: Bool -> Bool -> Bool
```
>Operators are not special
>Notice that we were able to find the type of `(||)` by wrapping it in parentheses. The `(||)` operator isn't “built into” the language: it's an ordinary function.
>
>The `(||)` operator “short circuits”: if its left operand evaluates to True, it doesn't evaluate its right operand. In most languages, short-circuit evaluation requires special support, but not in Haskell. We'll see why shortly.

Next, our function applies itself recursively. This is our first example of recursion, which we'll talk about in some detail shortly. 3 comments

Finally, our if expression spans several lines. We align the then and else branches under the if for neatness. So long as we use some indentation, the exact amount is not important. If we wish, we can write the entire expression on a single line.

```haskell
-- file: ch02/myDrop.hs
myDropX n xs = if n <= 0 || null xs then xs else myDropX (n - 1) (tail xs)
```

The length of this version makes it more difficult to read. We will usually break an if expression across several lines to keep the predicate and each of the branches easier to follow.

For comparison, here is a Python equivalent of the Haskell `myDrop`. The two are structured similarly: each decrements a counter while removing an element from the head of the list.

```python
def myDrop(n, elts):
    while n > 0 and elts:
        n = n - 1
        elts = elts[1:]
    return elts
```    

---
title: Understanding evaluation by example
files: []

---
In our description of `myDrop`, we have so far focused on surface features. We need to go deeper, and develop a useful mental model of how function application works. To do this, we'll first work through a few simple examples, until we can walk through the evaluation of the expression `myDrop 2 "abcd"`.

We've talked several times about substituting an expression for a variable, and we'll make use of this capability here. Our procedure will involve rewriting expressions over and over, substituting expressions for variables until we reach a final result. This would be a good time to fetch a pencil and paper, so that you can follow our descriptions by trying them yourself.

##Lazy evaluation
We will begin by looking at the definition of a simple, nonrecursive function.

>Source Code
>You should now have a file `RoundToEven` open up along with a Terminal window.

Here, `mod` is the standard modulo function. The first big step to understanding how evaluation works in Haskell is figuring out what the result of evaluating the expression `isOdd (1 + 2)` is.

Before we explain how evaluation proceeds in Haskell, let us recap the sort of evaluation strategy used by more familiar languages. First, evaluate the subexpression `1 + 2`, to give `3`. Then apply the `odd` function with `n` bound to `3`. Finally, evaluate `mod 3 2` to give `1`, and `1 == 1` to give `True`.

In a language that uses strict evaluation, the arguments to a function are evaluated before the function is applied. Haskell chooses another path: non-strict evaluation.

In Haskell, the subexpression `1 + 2` is not reduced to the value `3`. Instead, we create a “promise” that when the value of the expression `isOdd (1 + 2)` is needed, we'll be able to compute it. The record that we use to track an unevaluated expression is referred to as a *thunk*. This is all that happens: we create a thunk, and defer the actual evaluation until it's really needed. If the result of this expression is never subsequently used, we will not compute its value at all.

Non-strict evaluation is often referred to as lazy evaluation.

##A more involved example
Let us now look at the evaluation of the expression `myDrop 2 "abcd"`, where we use `print` to ensure that it will be evaluated.

```haskell
ghci> print (myDrop 2 "abcd")
"cd"
```

Our first step is to attempt to apply `print`, which needs its argument to be evaluated. To do that, we apply the function `myDrop` to the values `2` and `"abcd"`. We bind the variable `n` to the value `2`, and `xs` to `"abcd"`. If we substitute these values into `myDrop`'s predicate, we get the following expression.

```haskell
ghci> :type  2 <= 0 || null "abcd"
2 <= 0 || null "abcd" :: Bool
```

We then evaluate enough of the predicate to find out what its value is. This requires that we evaluate the `(||)` expression. To determine its value, the `(||)` operator needs to examine the value of its left operand first.

```haskell
ghci> 2 <= 0
False
```

Substituting that value into the `(||)` expression leads to the following expression.

```haskell
ghci> :type  False || null "abcd"
False || null "abcd" :: Bool
```

If the left operand had evaluated to `True`, `(||)` would not need to evaluate its right operand, since it could not affect the result of the expression. Since it evaluates to False, `(||)` must evaluate the right operand.

```haskell
ghci> null "abcd"
False
```

We now substitute this value back into the `(||)` expression. Since both operands evaluate to `False`, the `(||)` expression does too, and thus the predicate evaluates to `False`.

```haskell
ghci> False || False
False
```

This causes the if expression's `else` branch to be evaluated. This branch contains a recursive application of `myDrop`.

>Short circuiting for free
>Many languages need to treat the logical-or operator specially so that it short circuits if its left operand evaluates to True. In Haskell, `(||)` is an ordinary function: non-strict evaluation builds this capability into the language.
>
>In Haskell, we can easily define a new function that short circuits.
>
>```haskell
-- file: ch02/shortCircuit.hs
newOr a b = if a then a else b
>```
>
>If we write an expression like `newOr True (length [1..] > 0)`, it will not evaluate its second argument. (This is just as well: that expression tries to compute the length of an infinite list. If it were evaluated, it would hang ghci, looping infinitely until we killed it.) 7 comments
>
>Were we to write a comparable function in, say, Python, strict evaluation would bite us: both arguments would be evaluated before being passed to `newOr`, and we would not be able to avoid the infinite loop on the second argument.


---
title: aaaa
files: []

---
xxx
---
title: New Section 20
files: []

---

---
title: New Section 21
files: []

---

---
title: New Section 22
files: []

---
