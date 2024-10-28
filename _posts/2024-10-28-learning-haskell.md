---
layout: post
title:  "Learning Haskell/Functional Programming"
date:   2024-10-28 15:48:00 +1000
categories: haskell fp
tags: ["haskell", "fp", "notes"]
---

# Learning Haskell/Functional Programming

## Everything is an expression

Haskell has no statements, only expressions.

```haskell
2 + 2 -- expression resolving to 4
if (3 `mod` 5) == 2 then "TRUE" else "FALSE" -- expression resolving to "FALSE"
```

## Everything is a function

Operators are just functions that have a special setup to allow for infix.

```haskell
(+) 2 2 -- equivalent to 2 + 2

concatStrings a b = a ++ b

-- the following are equivalent uses
concatStrings "hello, " "world"
"hello, " `concatStrings` "world"
```

- Wrap an infix function in brackets (i.e. `(+)`) to make it a prefix function
- Wrap a prefix function in back-ticks to make it infix (i.e. ``concatStrings``)

## All function type definitions are written as though all functions are curried

```haskell
-- The addition (+) function has the following type definition
(+) :: Num a => a -> a -> a

-- Read as
-- 
-- function name :: Type typevar => takes param of type a 
-- and returns a function that takes param of type a that 
-- returns a value of type a
```

Curried functions are functions that take only one parameter. If a function requires more than one parameter then the function returns another function which takes a parameter and does for each additional parameter we need to take.

In Javascript this would look like:

```jsx
function add(a) {
	return function(b) {
		return a + b;
	}
}

// usage
var result = add(2)(2); // result = 4

var partiallyApplied = add(5); // equivalent to (b) => 5 + b
var result = partiallyApplied(5); // result = 10
```

## Typeclasses

Type-classes use the term class, however are more appropriately similar to interfaces. A type-class is basically a contract of what things you can do with the type.

For example, you can multiply two values that have the type-class of integer, but can’t multiply two values that have the type-class of boolean.

There are concrete type-classes and also “abstract” type-classes. For example, Integer is a concrete type-class because it represents a concrete value. But there are also abstract type-classes like `Ord` which provide the functionality necessary for a type to use functions like less-than, greater-than etc, but on their own do not represent a complete type.

## Lists

Lists in Haskell like most other functional languages are basically the same as how lists are implemented in LISP. Internally they are linked-lists of cons cells.

```haskell
let x = [1,2,3,4,5] -- a basic list

-- You can use the `:` operator to cons together values into a list
[1,2,3,4,5] == 1:2:3:4:5:[] -- True

-- get length of list x (5)
length x

-- get the element at index 0
x !! 0

-- get head of list
head x

-- get tail of list
tail x

-- append to front of list
0 : x -- [0,1,2,3,4,5]

-- append to end of list (note: must append a list to a list)
x ++ [6] -- [1,2,3,4,5,6]
```

## Lambda’s

Lambda’s are defined by using the `\` operator.

```haskell
let x = \x -> x + 2
x 5 -- results in 7

let x = [1,2,3,4,5]
map (\x -> x * x) x -- use lambda to map a function over each value in the list
```

## Functors

While everyone tries to explain functors, the best explanation I have come across is that a functor is anything that allows you to use the `fmap` function.

A more detailed explanation is that a Functor is a way to take a container with a typed value inside of it, apply a function to the internal value, and return a new container with the new value inside it.

This is important because the idea is that we’re not allowed to just grab the value out of the container, do something with it and put it back in the container.

i.e. if we are in javascript, we’re not allowed to do the following (this is not *pure*)

```jsx
function someFunction(a) {
	return a + 5;
}

var x = [1,2,3,4,5]; // arrays are a type of container

// NOT ALLOWED TO DO THIS
for (var i = 0; i < x.length; i++) {
	x[i] = someFunction(x[i]);
}

// WE ARE ALLOWED TO DO THIS
var mappedX = x.map((a) => a + 5);
```

In this case, `fmap` in Haskell and `map` in Javascript are the same.

<aside>
💡

The reason Haskell has both `fmap` and `map` is because `map` is designed only to work with lists and as such, its error message will report an error about lists which the language designers thought was much easier to understand for beginners. Functionally, `fmap` and `map` are equivalent but `fmap` is the generalised version.

> *You might ask why we need a separate `map` function. Why not just do away with the current list-only `map` function, and rename `fmap` to `map` instead? Well, that’s a good question. The usual argument is that someone just learning Haskell, when using `map` incorrectly, would much rather see an error about lists than about `Functor`.*
**Source:** **[https://wiki.haskell.org/Typeclassopedia#Functor](https://wiki.haskell.org/Typeclassopedia#Functor)
> 
</aside>

## Monads

The monad is probably one of the most confusing concepts in functional programming because people can’t help but over-complicate it when explaining it. In fact, there is a quote somewhere that the rule of learning monads is that you must write a blog-post or explainer for monads. Another quote is that the monad exists for developers to cosplay as mathematics PhD’s.

So instead explaining it using maths and category theory, lets instead explain it using Javascript!

In Javascript we have a thing called a `Promise`, which is just a wrapper around some async operation that may or may not have completed yet.

With a promise you can do a number of things:

```jsx
let x = new Promise((resolve, reject) => {
    setTimeout(() => resolve(console.log("hello world")), 1000);
});

x.then(() => {
	console.log("Promise resolved");
}).then(() => {
	console.log("Do something else");
}).catch(() => {
	console.error("Oh no! an error!");
});
```

In essence, what is happening is that every `then()` callback is executed in sequence, and the return value of the previous callback is passed as an argument to the next callback. On top of that, we also have the `catch()` callback which is executed if any error is encountered in any of the sequenced steps for handling.

Under the hood, every returned value from a `then()` callback is wrapped in a promise and the return value is given to the arguments of the new Promises function, so it is more accurate to say that each call to `then()` returns a new promise and applies the function to the new promise, thus returning a new promise of its own.

With this, Javascript’s `Promise`  is a monad.

A monad is essentially some container type that allows you to use a function called `bind()` , although in our Promises example we call `bind()` as `then()` .

### How do we use Monads in Haskell?

Haskell has the `>>=` operator which can make for some hard-to-read code.

```haskell
thing1 >>= (\x -> func1 x >>= (\y -> thing2 
       >>= (\_ -> func2 y >>= (\z -> return z)))) -- basically unreadable...

-- written in a way where we don't hate ourselves.
thing1  >>= \x ->
func1 x >>= \y ->
thing2  >>= \_ ->
func2 y >>= \z ->
return z
```

But we can also use the very nice syntactic sugar and use `do` notation which makes things easier to read/use.

```haskell
do {
  x <- thing1 ;
  y <- func1 x ;
  thing2 ;
  z <- func2 y ;
  return z
  }
```

<aside>
💡

*the curly braces and the semicolons are optional when the indentation rules are observed*

</aside>

## So what are these containers?

These containers are basically wrappers around a value/s with the intention of hiding away things like side-effects.

A common example of these kinds of containers is the `Maybe` container, which is called a number of different things in different languages.

- `Optional`
- `Nullable`

If we were to use Typescript as our example language:

```tsx
// root Maybe type
abstract class Maybe<T> {
    abstract bind<U>(func: (value: T) => Maybe<U>): Maybe<U>;
}

// Maybe has a value
class Just<T> extends Maybe<T> {
    constructor(private readonly value: T) {
        super();
    }

    bind<U>(func: (value: T) => Maybe<U>): Maybe<U> {
        return func(this.value);
    }
}

// No value in Maybe
class Nothing<T> extends Maybe<T> {
    bind<U>(func: (value: T) => Maybe<U>): Maybe<U> {
        return new Nothing<U>();
    }
}

// convenience function
function just<T>(value: T): Just<T> {
    return new Just(value);
}

// create our chain
const result = (new Just(5))
    .bind((a) => just(a + 2))
    .bind((b) => just(b + 3))
    .bind((c) => just(c + 4));

console.log(result); // result is (Just: { value: 14 })

```