---
layout: post
title:  "Crash Course of Dafny"
date:   2024-1-13
categories: PL
---

From https://github.com/dafny-lang/dafny. 
<!-- more -->

# Loop Invariants 
``` c++
function Fib (x : nat) : nat {
    if x < 2 then x else Fib(x-1) + Fib(x-2)
}

method make (n : nat) returns (x : int)
    ensures x == Fib(n)
{
    var i := 0;
    x := 0;
    var y := 1;
    while i < n 
        invariant 0 <= i <= n
        invariant x == Fib(i) && y == Fib(i+1)
    {
        i := i + 1;
        x, y := y, x + y;
    }
}

function Sum (n : nat) : nat{
    if n == 0 then 0 else n + Sum(n - 1)
}

method make_sum (n : nat) returns (x : nat)
ensures x == Sum (n)
{
    x := 0;
    var i := 0;
    while i < n
        invariant 0 <= i <= n
        invariant x == Sum(i)
    {
        x := x + i + 1;
        i := i + 1;
    }
}
```

# Binary Search 
```c++
method binarySearch (arr : array<int>, key : int) returns (r : int)
    requires forall i, j :: 0 <= i < j < arr.Length ==> arr[i] < arr[j]
    ensures r < 0 ==> forall i :: 0 <= i < arr.Length ==> key != arr[i]
    ensures r >= 0 ==> r < arr.Length && arr[r] == key
{
    var lo := 0;
    var hi := arr.Length;
    while lo < hi 
        invariant 0 <= lo <= hi <= arr.Length
        invariant forall i :: 0 <= i < lo ==> arr[i] < key
        invariant forall i :: hi <= i < arr.Length ==> arr[i] > key
    {
        var mid := (hi - lo) / 2 + lo;
        if arr[mid] > key {
            hi := mid;
        }
        else if arr[mid] < key {
            lo := mid + 1;
        }
        else {
            return mid;
        }
    }
    return -1;
}
```

# Sorting
```c++
datatype Color = Red | White | Blue

predicate Below (a : Color, b : Color) {
    a == Red || b == Blue || a == b
}

method sort_alg (arr : array<Color>)
    modifies arr
    ensures forall i, j :: 0 <= i < j < arr.Length ==> Below(arr[i], arr[j])
    ensures multiset(arr[0..arr.Length]) == multiset(old (arr[0..arr.Length]))
{
    var r, w, b := 0, 0, arr.Length;
    while w < b 
        invariant 0 <= r <= w <= b <= arr.Length
        invariant forall i :: 0 <= i < r ==> arr[i] == Red
        invariant forall i :: r <= i < w ==> arr[i] == White
        invariant forall i :: b <= i < arr.Length ==> arr[i] == Blue
        invariant multiset(arr[0..arr.Length]) == multiset(old (arr[0..arr.Length]))
    {
        match arr[w]
        case Red => 
            arr[r], arr[w] := arr[w], arr[r];
            r, w := r + 1, w + 1;
        case White => 
            w := w + 1;
        case Blue => 
            arr[w], arr[b - 1] := arr[b - 1], arr[w];
            b := b - 1;
    }
}
```