---
layout: post
title:  "Intro_to_PL (Week 3)"
date:   2023-12-30
categories: PL
---

# Functional Programming
A language is functional programming when
- avoiding mutation in most cases
- using functions as values 
<!-- more -->
## First-Class Functions
First-Class Functions means we can use functions whenever we use values. 

```ML
fun double_triple f =
    if f 7 
    then fn x => 2*x
    else fn x => 3*x
```

## Higher-Order Functions
A function that takes or returns a function. 

## Function Closure
Functions can use bindings from outside the function definition (in scope where function is defined)

Here is an example: 
```ML
fun A() = 
    let
        val x = 10  (* A local variable in A's scope *)
        
        (* B is a closure that uses x from A's scope *)
        fun B(y) = x + y  
    in
        B  (* A returns B as a closure *)
    end;
```
The closure B maintains access to the environment it was created in A's local scope, even after A has completed execution. 

# Anonymous Functions
Use keyword `fn`: `fn x => 3*x`



# Lexical Scope
**Lexical Scope** means the scope where function is defined. 

Function bodies can use bindings in the lexical scope. 

This is because a function value has two parts, which forms a pair: 
- the code
- the environment where the function is defined 

This pair is called **function closure**. For instance, the binding `fun f y = x + y` bound `f` to a closure.

Here is a quiz: 
```ML
fun f g = let val x = 9 in g() end
val x = 7
fun h() = x+1
val y = f h
```
It is x=8 under lexical scope and x=10 under dynamic scope. Under dynamic scope, the body of function h would end up "seeing" the local binding of x to 9 and if we removed this local binding (since it appears to have no purpose), then dynamic scope would lead to an undefined variable.

# Map, Filter, Fold
## Fold
`fold(f, acc, [x_1, x_2, x_3])` computes `f(f(f(acc, x_1), x_2), x_3)`

The implememtation is: 
```ML
(* ('a * 'b -> 'a) * 'a * 'b list -> 'a *)
fun fold (f, acc, xs) = 
    case xs of 
        [] => acc
        | x::xs => fold (f, f(acc, x), xs)
```

Here is a sample function checking whether all elements in `xs` satisfies function `g`, 
```ML
fun f (g, xs) = fold ((fn(x, y) => x andalso g y), true, xs)
```

# Currying
```ML
val f = fn x => fn y => fn z => z >= y andalso y >= x
val res = (((f 3) 4) 5)

(* Syntatic sugar *)
fun f x y z = z >= y andalso y >= x
```

Therefore, instead of writing `(((f 3) 4) 5)`, ML uses `f 3 4 5`. 

## Partial Application
We can take advantage of currying, 
```ML
val incre_all = List.map (fn x => x + 1)
```

## Combining Functions
```ML
val f = Math.sqrt o Real.fromInt o abs
val x = f ~4;
(* val it = 2.0 : real *)
```

We can also use pipeline operator: 
```ML
infix |> 
fun x |> f = f x
fun g i = i |> abs |> Real.fromInt |> Math.sqrt
```

We can also change currying and uncurrying using the following functions: 
```ML
(* ('a * 'b -> 'c) -> 'a -> 'b -> 'c *)
fun curry f x y = f (x, y)

(* ('a -> 'b -> 'c) -> 'a * 'b -> 'c *)
fun uncurry f (x, y) = f x y
```

# Mutable References
- Type `t ref`, where `t` is a type
- Use `ref e` to create a reference with initial content `e`
- `e1 := e2` to update the content
- `!e` to retrieve the content

An example for using `ref`: 
```ML
val x = ref (1, "hi")
val z = x
val _ = x := (3, "world")
val y = #1 (!z)
(* val y = 3 : int *)
```

# Callbacks

Clients can register to an event that provides a "callback" -- a function that gets called when an event happens. For example, a computer library might need to tell whether a client has pressed his key. The purpose of these libraries is to let multiple users register to some events using "callback". However, a library cannot tell whether a client needs extra messages to finish his computation. A function closure is ideal because a function type `t1 -> t2` does not specify any other variables it uses. 

Here is an example for callbacks. 
```ML
val onKeyEvent : (int -> unit) -> unit
val cbs : (int -> unit) list ref = ref []
fun onKeyEvent f = cbs := f::(!cbs) (* The only "public" binding *)
fun onEvent i =
    let fun loop fs =
        case fs of
        [] => ()
        | f::fs’ => (f i; loop fs’)
    in loop (!cbs) 
    end

val timesPressed = ref 0
val _ = onKeyEvent (fn _ => timesPressed := (!timesPressed) + 1)
fun printIfPressed i =
    onKeyEvent (fn j => if i=j
        then print ("you pressed " ^ Int.toString i ^ "\n")
        else ())
val _ = printIfPressed 4
val _ = printIfPressed 11
val _ = printIfPressed 23
```

# Abstract Data Types With Closures

```ML
datatype set = S of { insert : int -> set, 
                      member : int -> bool, 
                      size : unit -> int
                    }

val empty_set : set
```
