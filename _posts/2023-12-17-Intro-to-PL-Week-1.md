---
layout: post
title:  "Intro_to_PL (Week 1)"
date:   2023-12-17
categories: PL
---

# Static / Dynamic Environment

Static Environment checks type, which does not run the program.
<!-- more -->
Dynamic Environment calculates the value. 

``` ML
val x = 1;
(* Static Environment : x -> int *)
(* Dynamic Environment : x -> 1 *)

val y = 2;
(* Static Environment : x -> int, y -> int *)
(* Dynamic Environment : x -> 1, y -> 2 *)

val z = (x + y) + (y + 1);
(* Static Environment : x -> int, y -> int, z -> int *)
(* Dynamic Environment : x -> 1, y -> 2, z -> 6 *)

val abs_z = if z < 0 then 0 - z else z;
(* Static Environment : x -> int, y -> int, z -> int, abs_z -> int *)
(* Dynamic Environment : x -> 1, y -> 2, z -> 6, abs_z -> 6 *)
```

Expression: 
- syntax
- type checking rule
- evaluation rule

For Variable: 
- syntax: sequence of letters, digits, _, but not starting with digits. 
- type checking: look up at current static environment. **If not there, fail. **
- evaluation rule: look up at current dynamic environment.

REPL: Read Evaluations Print Loop

# Shadowing

``` ML
val x = 1;
(* Static Environment : x -> int *)
(* Dynamic Environment : x -> 1 *)

val y = 2;
(* Static Environment : x -> int, y -> int *)
(* Dynamic Environment : x -> 1, y -> 2 *)

val x = x + 1;
(* Static Environment : x -> int, y -> int *)
(* Dynamic Environment : x -> 1, y -> 2, z -> 2 *)
```

# Function

Syntax: fun $x(x_1 : t_1, x_2 : t_2, \cdots, x_n : t_n) = e$.

Evaluation: **a function is a value**.

Type checking: check in the static environment
- if $x$ has type $t_1 * t_2 * \cdots -> t$
- $x_i$ has type $t_i$.
- then $x(x_1 : t_1, x_2 : t_2, \cdots, x_n : t_n)$ has type $t$.  

``` ML
(* val pow = fn  : int * int -> int *)
fun pow(x : int, y : int) = 
    if y = 0
    then 1
    else pow(x, y - 1)
```

# Pair, Tuple and lists

## Pair

- Syntax : $(e_1, e_2)$
- Evaluation : a pair is a value
- Type Checking : $ta * tb$

``` ML
(* (int * int) * (int * int) -> int *)
fun sum(x : int * int, y : int * int) = 
    (#1 x) + (#2 x) + (#1 y) + (#2 y) 

fun div_mod(x : int, y : int) = 
    (x div y, x mod y)

fun sort_pair(x : int * int) = 
    if (#1 x < #2 x)
    then x
    else (#2 x, #1 x)
```

## Tuple
``` ML
(* int * (bool, int) *)
val x = (7, (true, 9));

(* bool *)
val y = #1 (#2 x)
```

## List
``` ML
val a = []
val a = [1, 2, 3]
val a = 0::a

(*[opening test.sml]                           │
val a = <hidden> : 'a list                     │
val a = <hidden> : int list                    │
val a = [0,1,2,3] : int list                   │
val it = () : unit*)

[6]::[[1, 2, 3], [1, 2]]

null []; (* true *)
hd [1, 2, 3] (* return 1 *)
tl [1, 2, 3] (* return [2, 3] *)

[(1, 2), (1, 3)] (* (int * int) list *)
```

## list functions 
``` ML
fun sum_list (xs : int list) = 
    if null xs
    then 0
    else hd xs + sum_list(tl xs)

fun count_down (x : int) = 
    if x = 0
    then []
    else x::count_down(x - 1)
```

# Let Expression

Syntax: let $b_1, b_2, \cdots, b_n$ in $e$ end ($b_i$ is a binding, $e$ is an expression)
``` ML
fun silly () = 
    let 
        val x = 1;
    in 
        (let val x = 2 in x + 1 end) + (let val y = x + 2 in y + 1 end)
    end

fun countup (x : int) = 
    let 
        fun count_from_to (from : int, to : int) = 
            if from = to
            then []
            else from::count_from_to(from + 1, to)
    in 
        count_from_to(1, x + 1)
    end
```

# Options
``` ML
fun max1(xs : int list) = 
    if null xs 
    then NONE
    else 
        let val tl_max = max1(tl xs)
        in if isSome tl_max andalso hd xs < valOf tl_max
            then tl_max
            else SOME(hd xs)
        end
```

# Boolean
- $e_1$ andalso $e_2$
- $e_1$ orelse $e_2$
- not $e_1$

# No Mutation
ML do not have mutations. ML cannot tell aliases, but it does not matter because there is no mutations. (E.g.: `tl` function returns an alias, which is faster than returning a copy of a list)
