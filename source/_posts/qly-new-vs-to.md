---
title: qly new vs to
date: 2021-03-04 21:08:49
updated: 2021-03-04 21:08:49
tags:
- qly
- compiler
- programming language design
categories:
- qly
---

Qly has both a `new` operator and a `to` operator. They look similar in some cases but actually have different use cases.

# Use `new` to create a new var with value of existing type
```
t[my-int int]
f[foo [x:my-int y:my-int]
  ...]
foo[new[my-int 3] new[my-int 4]] 
```
In this example, `foo` takes `my-int` as arg, however, `3` is a `int` but not `my-int`, so we use new to create a new instance of my-int

Another example is construct a struct:
```
t[point struct[x:int y:int]]
new[point [x:3 y:4]]
```
Here `[x:3 y:4]` is a general struct, and we'd like to use it to initialize a point struct, `new` is used for the exactly same reason.

# Use `to` to convert between different types
```
to[int8 32]
```
In this example we would like to construct a `int8` with an integer literal. qly integer literal that less than `2^31-1` has type `int32`, so, it need a conversion if we want a `int8`. This looks inefficient, but compiler would surely optimize it away to be a `int8` literal. We do not use `new` here because `int8` and `int32` are two different types without type def relationship, unlike the above example `my-int` is defined in terms of `int`.

`to` is also a generic function that can be specialized to define customized conversion method between type `X` and `Y`. 

# Convert Abstract type to concrete type with `to`
One treat abstract type as concrete type or vice versa. In this presumable example:
```
t[int]
t[int32:int raw-byte[4]]
t[int64:int raw-byte[8]]
```
If a function takes `int` and you have `int32` arg, you can use `int32` arg without conversion. If a function takes `int32` and you have a `int`, you'll need to call `to[int32 var-of-int]`. If `var-of-int` happens to be a `int32` kind of `int`, it just treat it as `int32` with no conversion. otherwise if it is a `int64`:
- if `to[from:int64 to:int32]` is defined, it will be called to get `int32`
- otherwise it will error

