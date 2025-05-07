---
layout: post
title:  "Intro_to_PL (Week 4)"
date:   2024-1-15
categories: PL
---


# Type Inference 
Analysis the constraints (e.g., `a > 0` infers `a : int`). 
<!-- more -->

Use **type variables** (e.g., ’a) for any unconstrained types in function arguments or results.

Here is an example: 
```ML
fun compose (f, g) = fn x => f (g x)
```

- The function `compose` has the type: `T1 * T2 -> T3`. 
- The anonymous function has the type: `T3 = T4 -> T5`.
- `g` has the type: `T4 -> T6`.
- `f` has the type: `T6 -> T7`.
- `T7 = T5`.
- `compose` has the type: `(T6 -> T5) * (T4 -> T6) -> (T4 -> T5)`.
- Using Type Variant: `('a -> 'b) * ('c -> 'a) -> ('c -> 'b)`.

## The Value Restriction
Only by Type Inference is not enough. 
```ML
val x = ref NONE (* type of r : ?.X1 option ref *)
val _ = x := SOME "hi"
val _ = 1 + valOf (!x)
```
This creates a dummy type for `x`, which cannot be used later. 

In general, ML will give a variable in a val-binding a polymorphic type only if the expression in the val-binding is a value or a variable. This is called the value restriction. In our example, `ref NONE` is a call to the function `ref`. Function calls are not variables or values.

Similarly, `val pairWithOne = List.map (fn x => (x,1))` does not work. 

# Mutual Inference
```ML
datatype t1 = Foo of int | Bar of t2
and t2 = Baz of string | Quux of t1
```

# Modual System
Syntax: `structure Name = struct bindings end`

```ML
structure MyLib = struct 
    val half_pi = Math.pi / 2.0

    fun myFunc x = x / 2.0
end
```

## Signature matching
`structure FOO :> BAR`

A signature's type binding for a function must be more specific than the type binding for the function in the module.

Here is an example, 
```ML
signature MySig =
sig
    datatype rational = Whole of int | Frac of int * int
    exception BadFrac
    val make_frac : int * int -> rational 
    val add : rational * rational -> rational
    val toString : rational -> string  
end

structure MyRational :> MySig =
struct
    datatype rational = Whole of int | Frac of int * int
    exception BadFrac
    fun make_frac (x,y) =
        if y = 0
        then raise BadFrac
        else if y < 0
        then Frac(~x,~y)
        else Frac(x,y)

    fun add (r1,r2) =
       case (r1,r2) of
           (Whole(i),Whole(j)) => Whole (i + j)
         | (Whole(i),Frac(j,k))  => Frac(j+k*i,k)
         | (Frac(j,k),Whole(i))  => Frac(j+k*i,k)
         | (Frac(a,b),Frac(c,d)) => Frac(a*d + b*c, b*d)

    fun toString r =
       let fun gcd (x,y) =
               if x=y
               then x
               else if x < y
               then gcd(x,y-x)
               else gcd(y,x)
           fun reduce r =
               case r of
                   Whole _ => r
                 | Frac(x,y) =>
                   if x=0
                   then Whole 0
                   else
                       let val d = gcd(abs x,y) in
                           if d=y
                           then Whole(x div d)
                           else Frac(x div d, y div d)
                       end
       in
           case reduce r of
               Whole i   => Int.toString i
             | Frac(a,b) => (Int.toString a) ^ "/" ^ (Int.toString b)
    end
end
```

# Equivalence

It is important to think whether some functions have side effects. 

The following code is not equivalent in ML: 
```ML
val y = 2
fun f1 (f, g) = (f x) + (f x)
fun f2 (f, g) = y * (f x)
```

This is because `f` might print out something or increment a variable. 
