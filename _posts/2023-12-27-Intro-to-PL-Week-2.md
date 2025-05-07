---
title : Intro_to_PL (Week 2)
tags : Programming Language
categories : Programming Language
date : 2023-12-27 09:00:00
---

# Build Compound Types

Three types: 
- Each of: Tuple
- One of: Option
- Self reference: List
<!-- more -->
## Records

``` ML
val x = {name="keke", id=2002}
```

## Tuples as Syntactic Sugar

```ML
(* val x = (true,"hi",3) : bool * string * int *)
val x = {1=true, 3=3, 2="hi"};
```

# Data Type

```ML
datatype myType = Twoints of int * int
                | Str of string
                | Pizza
```

`Twoints` is called a **constructor** of type (int * int) -> myType. 

Then any `myType` is constructed using one of the constructors. 

A value contains: 
- A tag for which constructor (e.g., `Twoints`).
- Corresponding data (e.g., (4, 7)).

## Case 
```ML
fun f x = 
    case x of 
          Pizza => 3
        | Str s => String.size s
        | Twoints(i1, i2) => i1 + i2
```

In the example here, `Pizza`, `Str s`, and `Twoints(i1, i2)` are **patterns** and the RHS are expressions. 

## Type Synonyms
Use the keyword `type`

## Polymorphic Datatypes
Syntax: 
```ML
datatype 'a option = NONE | SOME of 'a
datatype ('a, 'b) tree = 
      Node of 'a * ('a, 'b) tree * ('a, 'b) tree
    | Leaf of 'b
```

## Each of Pattern Matching
We can think of writing `val v = e` as a type of `case` expression. A function argument can also be a pattern, i.e., `fun f p = e`. 

Here is an example: 
```ML
(* A poor style of writing pattern matching *)
fun sum_triple tri = 
    case tri of 
        (x, y, z) => x + y + z

(* A good way of writing pattern matching *)
fun sum_triple tri = 
    let val (x, y, z) = tri
    in 
        x + y + z
    end

(* Final version *)
fun sum_triple (x, y, z) = 
    x + y + z
```

Interestingly, in the last example, it seems that it takes three arguments. Actually, all functions in ML take and return **exactly one argument**. 

## Function Patterns
We can also repeat the `fun` type: 
```ML
fun f p1 = e1
    | p2 = e2
    | p3 = e3
```

# Type Inference
ML can infer some of the type in functions. 
```ML
(* ML can infer it is int * 'a * int -> int *)
fun partial_sum (x, y, z) = 
    x + z
```

## Equality Types
`''a` is an equality type that can only be replaced by types that can use `=` (e.g., `string`, `int`). 

Here is an example: 
```ML
(* ''a * ''a -> string *)
fun same_thing (x, y) = 
    if x = y then "yes" else "no"
```

# Exceptions
Type of all exceptions: `exn`. 
```ML
(* Create an exception *)
exception MyFirstException
exception MySecException of int * int

raise MySecException (3, 4)

(* e1 handle ex => v2 *)
val x = hd [] handle MyFirstException => 10
```

# Tail Recursion
ML will first pop the caller before the call and allow the callee to reuse the stack. 
