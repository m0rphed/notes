+++
title = "Writing MiniML compiler to MS IL just to tinker with set-theoretic types"  
date = 2021-05-03T09:19:42+00:00
updated = 2021-05-03T09:19:42+00:00
draft = false

[taxonomies]
tags = ["MiniML", "Type Theory",]

[extra]
author = "Vic Khov"
+++
___
<!-- 
### Тема: Имплементация компилятора с выводом типов на языке F# для постановки эксперимента
# Ocaml и полиморфные варианты -->

- Как читать тайпчек-грамматику: https://mukulrathi.netlify.app/create-your-own-programming-language/intro-to-type-checking/
- Dynamic Type Inference: https://www.youtube.com/watch?v=WYEKk2CeXsI
- Dynamic Type Inference for Gradual Hindley-Milner Typing: https://arxiv.org/abs/1810.126191V

## Черновик

### Полиморфные Варианты (в OCaml)

В языке OCaml есть "Типы-Варианты / Варианты" (они же размеченные объединения в F#)

Их также иногда называют алгебраическими типами данных (Algebraic Data Types), они есть во многих ЯП, но устроены они могут быть по-разному (в OCaml реализация ADT - это и есть "варианты"), тем кто ранее не встречался с упомянутыми понятиями это может напоминать enum-ы.

Они позволяют программисту описать такой тип данных, который может иметь различные формы,
каждый из различных "вариантов" такой формы будет обозначен ("размечен") некоторым тегом (названием/словом).

```ocaml
type point = float * float
type shape =
  | Point of point
  | Circle of point * float (* center and radius *)
  | Rect of point * point (* lower-left and upper-right corners *)
```

https://cs3110.github.io/textbook/chapters/data/algebraic_data_types.html
___

Далее можно обрабатывать вариант `shape` также как и любой другой тип - передавать его в функции, присваивать ...

```ocaml
let someShape = Circle ((0., 0.), 1.)

let center = function
  | Point p -> p
  | Circle (p, _) -> p
  | Rect ((x1, y1), (x2, y2)) -> ((x2 +. x1) /. 2.0, (y2 +. y1) /. 2.0)

center someShape (* вернёт центр окружности: (0., 0.) *)
```
#### Полиморфные варианты
Представим более сложный пример:

Мы объявляем вариант описывающий виды некоторых животных, причём есть особый подтип животного, который имеет собственное имя

```ocaml
type animal = 
| Wolf
| Tiger
| Named of string
```
Так же мы с помощью варианта `ancestors` описываем различных предков человека (предки человека формально тоже являются ***некоторым видом*** животных)
```ocaml
type ancestors =
| HomoSapien
| Neanderthal
```
Теперь мы хотим получить валидное название для любого вида, и для этого мы вводим функцию `string_of_animal`:
```ocaml
let string_of_animal x = match x with
| Wolf -> "wolf"
| Tiger -> "tiger"
| Named name -> "It's name is " ^ name
```
Выведенный тип функции будет: `val string_of_animal : animal -> string = <fun>`
___

вызов функции

```ocaml
string_of_animal (Named "Cheburashka");;
```
вернёт нам:

`- : string = "It's name is Cheburashka"` всё работает ожидаемо,
но что если нам требуется функция,
которая сможет вернуть валидную строку как для любого животного,
так и для предков человека?
___
```ocaml
let string_of_animal x = match x with
| Wolf -> "wolf"
| Tiger -> "tiger"
| Named name -> "It's called " ^ name;;
| HomoSapien -> "homo sapien"
| Neanderthal -> "neanderthal"
(**
Error: This variant pattern is expected to have type firstHuman
       The constructor Dog does not belong to type firstHuman
*)
```
Т.к. обычные варианты не поддерживают полиморфизм, компилятор не сможет вывести обобщённый тип для аргумента `x` функции `string_of_animal`.

### Полиморфные варианты
Изначально полиморфные варианты планировалось добавить в качестве реализации "типов объединения" системы типизации в OCaml.
Типы объединения особенно удобны для случаев когда мы работаем с разными наборами полиморфных данных.

Переписав функцию `string_of_animal`, заменив "обычные варианты" на полиморфные,
компилятор сможет вывести обобщённый тип для аргумента:
```ocaml
let string_of_beast x = match x with
| `HomoSapien	-> "homo sapien"
| `Neanderthal	-> "neanderthal"
| `Dog			-> "dog"
| `Cat			-> "cat"
| `Named name 	-> "It's called " ^ name;;
```
Обобщённый тип:
```
val string_of_beast :
  [< `Cat | `Dog | `HomoSapien | `Named of string | `Neanderthal ]
    -> string = <fun>
```
При выводе типов union types позволяют получить более "точное" (finer grained) разбиение типов, чем при использовании обычных **ADT** (вариантов).

Однако существуют ограничения:
- нельзя использовать полиморфные варианты вместе с дженерик-типами
- сообщения об ошибках типизации очень длинные
- 

```
```

1) ошибки типиизации часто очень длинные (с этим не очень понятно что делать)
2) иногда выводятся не очень естественные типы
Пункт 2 как-то пофиксил Кастанья, и сказал, чтобы всё (3й пример) было зашибись нам нужен RTTI
Который никогда не прибудет в мейнстримный OCaml.
Поэтому было выбрано решение релизовать минимальное подмножество фщка с компиляцией куда-нить, где есть RTTI
И был выбран .NET а не JVM потому что студент более знаком с .NET

___
https://arxiv.org/pdf/1606.01106.pdf (раздел 1. Introduction)
### **Типы-Объединения в других ЯП**

В других языках также существуют "Типы объединения" (Union Types), семантика которых соответствует объединению множеств.

Например в TypeScript их можно объявить с помощью значка "`|`" прямо в аннотации функции:

```typescript
function printId(id: number | string) { // Объединение: <число> ∪ <строка>
  console.log(id);
}
```

При передаче аргумента несоответствующего объединению  
```typescript
printId (["union type constraint error"]);
// ожидаем ошибку типизации в "compile time"
```
будет ошибка:
```
Argument of type 'string[]' is not assignable
    to parameter of type 'string | number'.
  Type 'string[]' is not assignable to type 'string'.
```

Если попробовать вызвать метод который объявлен только у некоторых из причисленных в объединении типов 
```typescript
function printId(id: number | string) { // Объединение: <число> ∪ <строка>
  console.log(id.toUpperCase());
}
```
точно также будет ожидаемая ошибка:
```
Property 'toUpperCase' does not exist on type 'string | number'.
  Property 'toUpperCase' does not exist on type 'number'.
```

такой способ представления типов *интуитивно* ближе к представлению типов в качестве множеств, однако не для всех систем типов можно реализовать такой механизм.

### Semantic Subtyping

**WARNING: NOT READY**

The distinguishing feature of a type system using SemanticSubtyping is that it has a set-theoretic model, in which types correspond directly to sets of values.
Many recent type systems rely on a subtyping relation. Its definition generally depends on the type algebra, and on its intended use. We can distinguish two main approaches for defining subtyping: the syntactic approach and the semantic one. The syntactic approach -- by far the most widespread -- consists in defining the subtyping relation by axiomatising it in a formal system (a set of inductive or coinductive rules); in the semantic approach [...], instead, one starts with a model of the language and an interpretation of types as subsets of the model, then defines the subtyping relation as the inclusion of denoted sets, and, finally, when the relation is decidable, derives a subtyping algorithm from the semantic definition.



Утверждается, что система (теоретико-множественная система типов - set-theoretic type system) хорошо подходит для типизации полиморфных вариантов.

и выгодно отличается от того, который в настоящее время используется в OCaml.

**Note:**

1. Use the online reference of [Supported TeX Functions](https://katex.org/docs/supported.html)
