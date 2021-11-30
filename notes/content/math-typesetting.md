+++
title = "MiniML as environment for experimenting with set-theoretic types"  
date = 2021-05-03T09:19:42+00:00
updated = 2021-05-03T09:19:42+00:00
draft = false

[taxonomies]
tags = ["MiniML", "Type Theory",]

[extra]
author = "Vic Khov"
+++

### Тема: Имплементация компилятора с выводом типов на языке F# для постановки эксперимента

Утверждается, что система (теоретико-множественная система типов - set-theoretic type system) хорошо подходит для типизации полиморфных вариантов.

и выгодно отличается от того, который в настоящее время используется в OCaml.

```ocaml
let fib n =
  let rec aux acc n2 n1 = function
  | 1 -> acc
  | c -> aux ((n2 + n1) :: acc) n1 (n2 + n1) (c - 1)
  in
  List.rev(aux [1; 0] 0 1 (n - 1))
;;
```

**Note:**

1. Use the online reference of [Supported TeX Functions](https://katex.org/docs/supported.html)

### Examples

$$ \KaTeX $$
