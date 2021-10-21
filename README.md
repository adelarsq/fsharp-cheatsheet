# F# Cheatsheet 🔷

An updated cheatsheet for [F#](https://fsharp.org/).

This cheatsheet glances over some of the common syntax of F#.

Contents
--------
- [Comments](#Comments)  
- [Strings](#Strings)  
- [Basic Types and Literals](#BasicTypesAndLiterals)  
- [Loops](#Loops)  
- [Functions](#Functions)  
- [Pattern Matching](#PatternMatching)  
- [Collections](#Collections)  
- [Tuples and Records](#TuplesAndRecords)  
- [Discriminated Unions](#DiscriminatedUnions)  
- [Exceptions](#Exceptions)  
- [Classes and Inheritance](#ClassesAndInheritance)  
- [Interfaces and Object Expressions](#InterfacesAndObjectExpressions)  
- [Casting and Conversions](#CastingAndConversions)  
- [Active Patterns](#ActivePatterns)  
- [Compiler Directives](#CompilerDirectives)  
- [Useful functions](#UsefulFunctions)  
  - [Mapping functions](#MappingFunctions)  
  - [Grouping functions](#GroupingFunctions)  
  - [Array, List and Seq functions](#ArrayListAndSeqFunctions)  
- [Acknowledgments](#Acknowledgments)  

-------------------

<a name="Comments"></a>Comments
--------

Line comments start from `//` and continue until the end of the line. Block comments are placed between `(*` and `*)`.

```fsharp
// And this is line comment
(* This is block comment *)
```

[XML doc comments](https://docs.microsoft.com/dotnet/fsharp/language-reference/xml-documentation) come after `///` allowing us to use XML tags to generate documentation.    
    
```fsharp
/// The `let` keyword defines an (immutable) value
let result = 1 + 1 = 2
```

<a name="Strings"></a>Strings
-------

The F# `string` type is an alias for `System.String` type. See [Strings](https://docs.microsoft.com/dotnet/fsharp/language-reference/strings).

```fsharp
/// Create a string using string concatenation
let hello = "Hello" + " World"
```

Use *verbatim strings* preceded by `@` symbol to avoid escaping control characters (except escaping `"` by `""`).

```fsharp
let verbatimXml = @"<book title=""Paradise Lost"">"
```

We don't even have to escape `"` with *triple-quoted strings*.

```fsharp
let tripleXml = """<book title="Paradise Lost">"""
```

*Backslash strings* indent string contents by stripping leading spaces.

```fsharp
let poem = 
    "The lesser world was daubed\n\
     By a colorist of modest skill\n\
     A master limned you in the finest inks\n\
     And with a fresh-cut quill."
```

[Interpolated strings](https://docs.microsoft.com/dotnet/fsharp/language-reference/interpolated-strings) let you write code in "holes" inside of a string literal:

```fsharp
let name = "Phillip"
let age = 30
printfn $"Name: {name}, Age: {age}"

let str = $"A pair of braces: {{}}"
printfn $"Name: %s{name}, Age: %d{age}" // typed
```

<a name="BasicTypesAndLiterals"></a>Basic Types and Literals
------------------------

Most numeric types have associated suffixes, e.g., `uy` for unsigned 8-bit integers and `L` for signed 64-bit integer.

```fsharp
let b, i, l, ul = 86uy, 86, 86L, 86UL

// val ul: uint64 = 86UL
// val l: int64 = 86L
// val i: int = 86
// val b: byte = 86uy
```

Other common examples are `F` or `f` for 32-bit floating-point numbers, `M` or `m` for decimals, and `I` for big integers.

```fsharp
let s, f, d, bi = 4.14F, 4.14, 0.7833M, 9999I

// val bi: System.Numerics.BigInteger = 9999
// val d: decimal = 0.7833M
// val f: float = 4.14
// val s: float32 = 4.14f
```

See [Literals](https://docs.microsoft.com/dotnet/fsharp/language-reference/literals) for complete reference.

<a name="BasicTypesAndLiterals"></a>Printing Things
------------------------

Print things to console with `printfn`:

```fsharp
printfn "Hello, World"

printfn $"The time is {System.DateTime.Now}"
```

You can also use `Console.WriteLine`:
```fsharp
open System

Console.WriteLine $"The time is {System.DateTime.Now}"
```

Constrain types with `%d`, `%s`, and print structured values with `%A`:

```fsharp
let data = [1..10]

printfn $"The numbers %d{1} to %d{10} are %A{data}"
```

Omit holes and apply arguments:

```fsharp
printfn "The numbers %d to %d are %A" 1 10 data
```

See [Plaintext Formatting](https://docs.microsoft.com/dotnet/fsharp/language-reference/plaintext-formatting)

<a name="Loops"></a>Loops
---------

### for...in

[For loops](https://docs.microsoft.com/dotnet/fsharp/language-reference/loops-for-in-expression):

```fsharp
let list1 = [1; 5; 100; 450; 788]

for i in list1 do
    printf "%d" i           // 1 5 100 450 788

let seq1 = seq { for i in 1 .. 10 -> (i, i * i) }

for (a, asqr) in seq1 do
    // 1 squared is 1
    // ...
    // 10 squared is 100
    printfn "%d squared is %d" a asqr

for i in 1 .. 10 do
    printf "%d " i          // 1 2 3 4 5 6 7 8 9 10

// for i in 10 .. -1 .. 1 do
for i = 10 downto 1 do
    printf "%i " i          // 10 9 8 7 6 5 4 3 2 1

for i in 1 .. 2 .. 10 do
    printf "%d " i             // 1 3 5 7 9

for c in 'a' .. 'z' do
    printf "%c " c          // a b c ... z

// Using of a wildcard character (_)
// when the element is not needed in the loop.
let mutable count = 0

for _ in list1 do
    count <- count + 1
```

### while...do

[While loops](https://docs.microsoft.com/dotnet/fsharp/language-reference/loops-while-do-expression):

```fsharp
let mutable mutVal = 0
while mutVal < 10 do        // while (not) test-expression do
    mutVal <- mutVal + 1
```

<a name="Functions"></a>Functions
---------

The [`let`](https://docs.microsoft.com/dotnet/fsharp/language-reference/functions/let-bindings) keyword also defines named functions.

```fsharp
let pi () = 3.14159 // function with no arguments. () is called unit type
pi ()               // it's necessary to use () to call the function

let negate x = x * -1 
let square x = x * x 
let print x = printfn $"The number is: %d{x}"

let squareNegateThenPrint x = 
    print (negate (square x)) 
```

Double-backtick identifiers are handy to improve readability especially in unit testing:

```fsharp
let ``square, negate, then print`` x = 
    print (negate (square x)) 
```

### Pipe operator

The pipe operator `|>` is used to chain functions and arguments together:

```fsharp
let squareNegateThenPrint x = 
    x |> square |> negate |> print
```

This operator is essential in assisting the F# type checker by providing type information before use:

```fsharp
let sumOfLengths (xs : string []) = 
    xs 
    |> Array.map (fun s -> s.Length)
    |> Array.sum
```

### Composition operator

The composition operator `>>` is used to compose functions:

```fsharp
let squareNegateThenPrint = 
    square >> negate >> print
```
  
<a name="PatternMatching"></a>Pattern Matching
----------------

Pattern matching is primarily through `match` keyword;

```fsharp
let rec fib n =
    match n with
    | 0 -> 0
    | 1 -> 1
    | _ -> fib (n - 1) + fib (n - 2)
```

Use `when` to create filters or guards on patterns:

```fsharp
let sign x = 
    match x with
    | 0 -> 0
    | x when x < 0 -> -1
    | x -> 1
```

Pattern matching can be done directly on arguments:

```fsharp
let fst (x, _) = x
```

or implicitly via `function` keyword:

```fsharp
/// Similar to `fib`; using `function` for pattern matching
let rec fib2 = function
    | 0 -> 0
    | 1 -> 1
    | n -> fib2 (n - 1) + fib2 (n - 2)
```

See [Pattern Matching](https://docs.microsoft.com/en-us/dotnet/fsharp/language-reference/pattern-matching).

<a name="Collections"></a>Collections
-----------

### Lists

[*Lists*](https://docs.microsoft.com/dotnet/fsharp/language-reference/lists) are immutable collection of elements of the same type.

```fsharp
// Lists use square brackets and `;` delimiter
let list1 = ["a"; "b"]

// :: is prepending
let list2 = "c" :: list1

// @ is concat    
let list3 = list1 @ list2   

// Recursion on list using (::) operator
let rec sum list = 
    match list with
    | [] -> 0
    | x :: xs -> x + sum xs
```

### Arrays

[*Arrays*](https://docs.microsoft.com/dotnet/fsharp/language-reference/arrays) are fixed-size, zero-based, mutable collections of consecutive data elements.

```fsharp
// Arrays use square brackets with bar
let array1 = [| "a"; "b" |]

// Indexed access using dot
let first1 = array1.[0]   
let first2 = array1[0]    // F# 6
```
      
### Sequences == IEnumerable

A *sequence* is a logical series of elements of the same type. Individual sequence elements are computed only as required, so a sequence can provide better performance than a list in situations in which not all the elements are used.

```fsharp
// Sequences can use yield and contain subsequences
seq {
    // "yield" adds one element
    yield 1
    yield 2

    // "yield!" adds a whole subsequence
    yield! [5..10]
}
```

The `yield` can normally be omitted:

```fsharp
// Sequences can use yield and contain subsequences
seq {
    1
    2
    yield! [5..10]
}
```

### Mutable Dictionaries (from BCL)

Create a dictionary, add two entries, remove an entry, lookup an entry

```fsharp
open System.Collections.Generic

let inventory = Dictionary<string, float>()

inventory.Add("Apples", 0.33)
inventory.Add("Oranges", 0.5)

inventory.Remove "Oranges"

// Read the value. If not exists - throw exception.
let bananas1 = inventory.["Apples"]
let bananas2 = inventory["Apples"]   // F# 6
```

Additional F# syntax:

```fsharp
// Generic type inference with Dictionary
let inventory = Dictionary<_,_>()   // or let inventory = Dictionary()

inventory.Add("Apples", 0.33)
```

### dict == IDictionary in BCL

*dict* creates immutable dictionaries. You can’t add and remove items to it.

```fsharp
open System.Collections.Generic

let inventory : IDictionary<string, float> =
    ["Apples", 0.33; "Oranges", 0.23; "Bananas", 0.45]
    |> dict

let bananas = inventory.["Bananas"]     // 0.45
let bananas2 = inventory["Bananas"]     // 0.45, F# 6

inventory.Add("Pineapples", 0.85)       // System.NotSupportedException
inventory.Remove("Bananas")             // System.NotSupportedException
```

Quickly creating full dictionaries:

```
[ "Apples", 10; "Bananas", 20; "Grapes", 15 ] |> dict |> Dictionary
```

### Map

*Map* is an immutable key/value lookup. Allows safely add or remove items.

```fsharp
let inventory =
    Map ["Apples", 0.33; "Oranges", 0.23; "Bananas", 0.45]

let apples = inventory.["Apples"]
let pineapples = inventory.["Pineapples"]   // KeyNotFoundException

let newInventory =              // Creates new Map
    inventory
    |> Map.add "Pineapples" 0.87
    |> Map.remove "Apples"
```

Safely access a key in a *Map* by using *TryFind*. It returns a wrapped option:

```fsharp
let inventory =
    Map ["Apples", 0.33; "Oranges", 0.23; "Bananas", 0.45]

inventory.TryFind "Apples"      // option = Some 0.33
inventory.TryFind "Unknown"     // option = None
```

Useful Map functions include `map`, `filter`, `partition`:

```fsharp
let cheapFruit, expensiveFruit =
    inventory
    |> Map.partition(fun fruit cost -> cost < 0.3)
```

### Dictionaries, dict, or Map?

* Use *Map* as your default lookup type:
    * It’s immutable
    * Has good support for F# tuples and pipelining.

* Use the *dict* function
    * Quickly generate an *IDictionary* to interop with BCL code.
    * To create a full Dictionary.

* Use *Dictionary*:
    * If need a mutable dictionary.
    * Need specific performance requirements. (Example: tight loop performing
      thousands of additions or removals).

### Generating lists

The same list `[ 1; 3; 5; 7; 9 ]` can be generated in various ways.

```fsharp
[ 1; 3; 5; 7; 9 ]
[ 1..2..9 ]
[ for i in 0..4 -> 2 * i + 1 ]
List.init 5 (fun i -> 2 * i + 1)
```

The array `[| 1; 3; 5; 7; 9 |]` can be generated similarly:

```fsharp
[| 1; 3; 5; 7; 9 |]
[| 1..2..9 |]
[| for i in 0..4 -> 2 * i + 1 |]
Array.init 5 (fun i -> 2 * i + 1)
```

### Functions on collections

Lists and arrays have comprehensive functions for manipulation.

  - `List.map` transforms every element of the list (or array)
  - `List.iter` iterates through a list and produces side effects

These and other functions are covered below. All these operations are also available for sequences. 

<a name="TuplesAndRecords"></a>Tuples and Records
------------------
A *tuple* is a grouping of unnamed but ordered values, possibly of different types:

```fsharp
// Tuple construction
let x = (1, "Hello")

// Triple
let y = ("one", "two", "three") 

// Tuple deconstruction / pattern
let (a', b') = x
```

The first and second elements of a tuple can be obtained using `fst`, `snd`, or pattern matching:

```fsharp
let c' = fst (1, 2)
let d' = snd (1, 2)
  
let print' tuple =
    match tuple with
    | (a, b) -> printfn "Pair %A %A" a b
```

*Records* represent simple aggregates of named values, optionally with members:

```fsharp
// Declare a record type
type Person = { Name : string; Age : int }

// Create a value via record expression
let paul = { Name = "Paul"; Age = 28 }

// 'Copy and update' record expression
let paulsTwin = { paul with Name = "Jim" }
```

Records can be augmented with properties and methods:

```fsharp
type Person with
     member x.Info = (x.Name, x.Age)
```

Records are essentially sealed classes with extra topping: default immutability, structural equality, and pattern matching support.

```fsharp
let isPaul person =
    match person with
    | { Name = "Paul" } -> true
    | _ -> false
```

<a name="Recursive Functions"></a>Recursive Functions
----------------

The `rec` keyword is used together with the `let` keyword to define a recursive function:

```fsharp
let rec fact x =
    if x < 1 then 1
    else x * fact (x - 1)
```

*Mutually recursive* functions (those functions which call each other) are indicated by `and` keyword:

```fsharp
let rec even x =
   if x = 0 then true 
   else odd (x - 1)

and odd x =
   if x = 0 then false
   else even (x - 1)
```

<a name="DiscriminatedUnions"></a>Discriminated Unions
--------------------

*Discriminated unions* (DU) provide support for values that can be one of a number of named cases, each possibly with different values and types.

```fsharp
type Tree<'T> =
    | Node of Tree<'T> * 'T * Tree<'T>
    | Leaf


let rec depth input =
    match input with
    | Node(l, _, r) -> 1 + max (depth l) (depth r)
    | Leaf -> 0
```

F# Core has a few built-in discriminated unions for error handling, e.g., [Option](http://msdn.microsoft.com/en-us/library/dd233245.aspx) and [Choice](http://msdn.microsoft.com/en-us/library/ee353439.aspx).

```fsharp
let optionPatternMatch input =
    match input with
    | Some i -> printfn "input is an int=%d" i
    | None -> printfn "input is missing"
```

Single-case discriminated unions are often used to create type-safe abstractions with pattern matching support:

```fsharp
type OrderId = Order of string

// Create a DU value
let orderId = Order "12"

// Use pattern matching to deconstruct single-case DU
let (Order id) = orderId
```

<a name="Exceptions"></a>Exceptions
----------
The `failwith` function throws an exception of type `Exception`.

```fsharp
let divideFailwith x y =
    if y = 0 then 
        failwith "Divisor cannot be zero." 
        else x / y
```

Exception handling is done via `try/with` expressions.

```fsharp
let divide x y =
    try
        Some (x / y)
    with :? System.DivideByZeroException -> 
        printfn "Division by zero!"
        None
```
  
The `try/finally` expression enables you to execute clean-up code even if a block of code throws an exception. Here's an example which also defines custom exceptions.

```fsharp
exception InnerError of string
exception OuterError of string
  
let handleErrors x y =
   try 
       try 
           if x = y then raise (InnerError("inner"))
           else raise (OuterError("outer"))
       with InnerError(str) -> 
          printfn "Error1 %s" str
   finally
       printfn "Always print this."
```

<a name="ClassesAndInheritance"></a>Classes and Inheritance
-----------------------
This example is a basic class with (1) local let bindings, (2) properties, (3) methods, and (4) static members.

```fsharp
type Vector(x: float, y: float) =
    let mag = sqrt(x * x + y * y)               // (1) - local let binding

    member this.X = x                           // (2) property
    member this.Y = y                           // (2) property
    member this.Mag = mag                       // (2) property

    member this.Scale(s) =                       // (3) method
        Vector(x * s, y * s)

    static member (+) (a : Vector, b : Vector) = // (4) static method
        Vector(a.X + b.X, a.Y + b.Y)
```

Call a base class from a derived one:

```fsharp
type Animal() =
    member _.Rest() = ()
           
type Dog() =
    inherit Animal()
    member _.Run() =
        base.Rest()
```

<a name="InterfacesAndObjectExpressions"></a>Interfaces and Object Expressions
---------------------------------
Declare `IVector` interface and implement it in `Vector'`.

```fsharp
type IVector =
    abstract Scale : float -> IVector

type Vector(x, y) =
    interface IVector with
        member __.Scale(s) =
            Vector(x * s, y * s) :> IVector
            
    member __.X = x
    
    member __.Y = y
```

Another way of implementing interfaces is to use *object expressions*.

```fsharp
type ICustomer =
    abstract Name : string
    abstract Age : int

let createCustomer name age =
    { new ICustomer with
        member __.Name = name
        member __.Age = age }
```

<a name="CastingAndConversions"></a>Casting and Conversions
---------------

```fsharp
int 3.1415     // float to int = 3
int "3"        // string to int = 3
float 3        // int to float = 3.0
float "3.1415" // string to float = 3.1415
string 3       // int to string = "3"
string 3.1415  // float to string = "3.1415"
```

*Upcasting* is denoted by `:>` operator.

```fsharp
let dog = Dog() 
let animal = dog :> Animal
```

In many places type inference applies upcasting automatically:

```fsharp
let exerciseAnimal (animal: Animal) = () 

let dog = Dog()

exerciseAnimal dog   // no need to upcast dog to Animal
```

*Dynamic downcasting* (`:?>`) might throw an `InvalidCastException` if the cast doesn't succeed at runtime.

```fsharp
let shouldBeADog = animal :?> Dog
```

<a name="ActivePatterns"></a>Active Patterns
---------------

*Complete active patterns*:

```fsharp
let (|Even|Odd|) i = 
  if i % 2 = 0 then Even else Odd

let testNumber i =
    match i with
    | Even -> printfn "%d is even" i
    | Odd -> printfn "%d is odd" i
```

*Parameterized, partial active patterns*:

```fsharp
let (|DivisibleBy|_|) divisor n = 
  if n % divisor = 0 then Some DivisibleBy else None

let fizzBuzz input =
    match input with
    | DivisibleBy 3 & DivisibleBy 5 -> "FizzBuzz" 
    | DivisibleBy 3 -> "Fizz" 
    | DivisibleBy 5 -> "Buzz" 
    | i -> string i
```

*Partial active patterns* share the syntax of parameterized patterns but their active recognizers accept only one argument.

<a name="CompilerDirectives"></a>Compiler Directives
-------------------

Load another F# source file into F# Interactive (`dotnet fsi`).

```fsharp
#load "../lib/StringParsing.fs"
```

Reference a .NET package:

```fsharp
#r "nuget: FSharp.Data"                // latest non-beta version
#r "nuget: FSharp.Data,Version=4.2.2"  // specific version
```

Specifying a package source:

```fsharp
#i "nuget: https://my-remote-package-source/index.json"

#i """nuget: C:\path\to\my\local\source"""
```

Reference a specific .NET assembly file:

```fsharp
#r "../lib/FSharp.Markdown.dll"
```

Include a directory in assembly search paths:

```fsharp
#I "../lib"
#r "FSharp.Markdown.dll"
```

Other important directives are conditional execution in FSI (`INTERACTIVE`), conditional for compiled code (`COMPILED`) and querying current directory (`__SOURCE_DIRECTORY__`).

```fsharp
#if INTERACTIVE
let path = __SOURCE_DIRECTORY__ + "../lib"
#else
let path = "../../../lib"
#endif
```


<a name="UsefulFunctions"></a>Useful functions
-------------------

<a name="MappingFunctions"></a>Mapping functions
-------------------

#### map (Array, List, Seq)

Converts all the items in a collection from one shape to another shape.
Always returns the same number of items in the output collection as were passed in.

```fsharp
// [2; 4; 6; 8; 10; 12; 14; 16; 18; 20]
[1 .. 10] |> List.map (fun n -> n * 2)

type Person = { Name : string; Town : string }

let persons =
    [
        { Name = "Isaak"; Town = "London" }
        { Name = "Sara"; Town = "Birmingham" }
        { Name = "Tim"; Town = "London" }
        { Name = "Michelle"; Town = "Manchester" }
    ]

// ["London"; "Birmingham"; "London"; "Manchester"]
persons |> List.map (fun person -> person.Town)
```

#### map2, map3 (Array, List, Seq)

*map2* and *map3* are variations of *map* that take multiple lists.
 The collections must have equal lengths, except for `Seq.map2` and `Seq.map3` where extra elements are ignored.
 
```fsharp
let list1 = [1; 2; 3]
let list2 = [4; 5; 6]
let list3 = [7; 8; 9]

// [5; 7; 9]
(list1, list2) ||> List.map2 (fun x y -> x + y)

// [12; 15; 18]
(list1, list2, list3) |||> List.map3 (fun x y z -> x + y + z) 
```

#### mapi, mapi2 (Array, List, Seq)

*mapi* and *mapi2* - in addition to the element, the function needs to be
passed the index of each element. The only difference `mapi2` and `mapi` is that `mapi2` works
with two collections.

```fsharp
let list1 = [9; 12; 53; 24; 35]

// [(0, 9); (1, 12); (2, 53); (3, 24); (4, 35)]
list1 |> List.mapi (fun x i -> (x, i))

// [9; 13; 55; 27; 39]
list1 |> List.mapi (fun x i -> x + i)

let list1 = [9; 12; 3]
let list2 = [24; 5; 2]

// [0; 17; 10]
(list1, list2) ||> List.mapi2 (fun i x y -> (x + y) * i) 
```

### indexed (Array, List, Seq)

Returns a new collection whose elements are the corresponding elements of the input
paired with the index (from 0) of each element.

```fsharp
let list1 = [23; 5; 12]

// [(0, 23); (1, 5); (2, 12)]
let result = list1 |> Array.indexed
```

### iter, iter2, iteri, iteri2 (Array, List, Seq)

*iter* is the same as a `for` loop, the function that you pass in must return unit.

```fsharp
let list1 = [1; 2; 3]
let list2 = [4; 5; 6]

// Prints: 1; 2; 3; 
list1 |> List.iter (fun x -> printf "%d; " x)

// Prints: (1 4); (2 5); (3 6);
(list1, list2) ||> List.iter2 (fun x y -> printf "(%d %d); " x y)

// Prints: ([0] = 1); ([1] = 2); ([2] = 3);
list1 |> List.iteri (fun i x -> printf "([%d] = %d); " i x)

// Prints: ([0] = 1 4); ([1] = 2 5); ([2] = 3 6);
(list1, list2) ||> List.iteri2 (fun i x y -> printf "([%d] = %d %d); " i x y) 
```

### collect (Array, List, Seq)

*collect* runs a specified function on each element and then collects the elements
generated by the function and combines them into a new collection.

```fsharp
// [0; 1; 0; 1; 2; 3; 4; 5; 0; 1; 2; 3; 4; 5; 6; 7; 8; 9; 10]
let list1 = [1; 5; 10]
let list2 = [1; 2; 3]

list1 |> List.collect (fun elem -> [0 .. elem])

// [1; 2; 3; 2; 4; 6; 3; 6; 9]
list2 |> List.collect (fun x -> [for i in 1..3 -> x * i])

// Example 3
type Customer =
    {
        Id: int
        OrderId: int list
    }

let c1 = { Id = 1; OrderId = [1; 2]}
let c2 = { Id = 2; OrderId = [43]}
let c3 = { Id = 5; OrderId = [39; 56]}
let customers = [c1; c2; c3]

// [1; 2; 43; 39; 56]
let orders = customers |> List.collect(fun c -> c.OrderId)
```

### pairwise (Array, List, Seq)

*pairwise* takes a collection and returns a new list of tuple pairs of
the original adjacent items.

```fsharp
let list1 = [1; 30; 12; 20]

//  [(1, 30); (30, 12); (12, 20)]
list1 |> List.pairwise

// [31.0; 11.0; 21.0]
[ DateTime(2010,5,1)
  DateTime(2010,6,1)
  DateTime(2010,6,12)
  DateTime(2010,7,3) ]
|> List.pairwise
|> List.map(fun (a, b) -> b - a)
|> List.map(fun time -> time.TotalDays)
```

### windowed (Array, List, Seq)

Returns a list of sliding windows containing elements drawn from the input
collection. Each window is returned as a fresh collection. Unlike *pairwise* 
the windows are collections, not tuples.

```fsharp
// [['a'; 'b'; 'c']; ['b'; 'c'; 'd']; ['c'; 'd'; 'e']]
['a'..'e'] |> List.windowed 3
```

<a name="GroupingFunctions"></a>Grouping functions
-------------------

### groupBy (Array, List, Seq)    == GroupBy() in LINQ

*groupBy* works exactly as the LINQ version does. The output is a collection of simple tuples.
The first element of the tuple is the key, and the second element is the collection of items
in that group.

```fsharp
type Person =
    {
        Name: string
        Town: string
    }

let persons =
    [ { Name = "Isaak"; Town = "London" }
      { Name = "Sara"; Town = "Birnmingham" }
      { Name = "Tim"; Town = "London" }
      { Name = "Michelle"; Town = "Manchester" } ]

// [("London", [{ Name = "Isaak"; Town = "London" }; { Name = "Tim"; Town = "London" }]);
//  ("Birnmingham", [{ Name = "Sara"; Town = "Birnmingham" }]);
//  ("Manchester", [{ Name = "Michelle"; Town = "Manchester" }])]
persons |> List.groupBy (fun person -> person.Town)
```

### countBy (Array, List, Seq)

A useful derivative of *groupBy* is *countBy*. This has a similar signature, but instead of
returning the items in the group, it returns the number of items in each group.

```fsharp
type Person = { Name: string; Town: string }

let persons =
    [
        { Name = "Isaak"; Town = "London" }
        { Name = "Sara"; Town = "Birnmingham" }
        { Name = "Tim"; Town = "London" }
        { Name = "Michelle"; Town = "Manchester" }
    ]

// [("London", 2); ("Birnmingham", 1); ("Manchester", 1)]
persons |> List.countBy (fun person -> person.Town)
```

### partition (Array, List)

*partition* use predicate and a collection; it returns two collections,
partitioned based on the predicate:

```fsharp
// Tupled result in two lists
let londonPersons, otherPersons =
    persons |> List.partition(fun p -> p.Town = "London")
```

If there are no matches for either half of the split, an empty collection is
returned for that half.

### chunkBySize (Array, List, Seq)

*chunkBySize* groups elements into arrays (chunks) of a given size.

```fsharp
let list1 = [33; 5; 16]

// int list list = [[33; 5]; [16]]
let chunkedLst = list1 |> List.chunkBySize 2
```

### splitAt (Array, List)

*splitAt* splits an Array (List) into two parts at the index you specify.
The first part ends just before the element at the given index;
the second part starts with the element at the given index.

```fsharp
let xs = [| 1; 2; 3; 4; 5 |]

let left1, right1 = xs |> Array.splitAt 0   // [||] and [|1; 2; 3; 4; 5|]
let left2, right2 = xs |> Array.splitAt 1   // [|1|] and [|2; 3; 4; 5|]
let left3, right3 = xs |> Array.splitAt 5   // [|1; 2; 3; 4; 5|] and [||]
let left4, right4 = xs |> Array.splitAt 6   // InvalidOperationException
```

### splitInto (Array, List, Seq)

Splits the input collection into at most count chunks.

```fsharp
// [[1; 2; 3; 4]; [5; 6; 7]; [8; 9; 10]]
// note that the first chunk has four elements
[1..10] |> List.splitInto 3

// [[1; 2; 3]; [4; 5; 6]; [7; 8]; [9; 10]]
[1..10] |> List.splitInto 4

// [[1; 2]; [3; 4]; [5; 6]; [7; 8]; [9]; [10]]
[1..10] |> List.splitInto 6
```

## Aggregate functions
-------------------

Aggregate functions take a collection of items and merge them into a smaller
collection of items (often just one).

### sum, average, min, max      (Array, List, Seq)

All of these functions are specialized versions of a more generalized function *fold*.

```fsharp
let numbers = [1.0 .. 10.0]

let total = numbers |> List.sum         // 55.0
let average = numbers |> List.average   // 5.5
let max = numbers |> List.max           // 10.0
let min = numbers |> List.min           // 1.0
```

##  Miscellaneous functions
-------------------

### find (Array, List, Seq)    == Single() in LINQ

*find* - finds the first element that matches a given condition.

```fsharp
let isDivisibleBy number elem = elem % number = 0

let input = [1 .. 10]

input |> List.find (isDivisibleBy 5)    // 5
input |> List.find (isDivisibleBy 11)   // KeyNotFoundException
```

### findBack (Array, List, Seq) 

```fsharp
let isDivisibleBy number elem = elem % number = 0

let input = [1 .. 10]

input |> List.findBack (isDivisibleBy 4)    // 8
input |> List.findBack (isDivisibleBy 11)   // KeyNotFoundException
```

### findIndex (Array, List, Seq)

```fsharp
let isDivisibleBy number elem = elem % number = 0

let input = [1 .. 10]

input |> List.findIndex (isDivisibleBy 5)   // 4
input |> List.findIndex (isDivisibleBy 11)  // KeyNotFoundException
```

### findIndexBack (Array, List, Seq)

```fsharp
let isDivisibleBy number elem = elem % number = 0

let input = [1..10]

input |> List.findIndexBack (isDivisibleBy 3)   // 8
input |> List.findIndexBack (isDivisibleBy 11)  // KeyNotFoundException
```

### head, last, tail, item (Array, List, Seq)

Returns the first, last and all-but-first items in the collection.

```fsharp
let input = [15..22]

input |> List.head    // 15
input |> List.last    // 22
input |> List.tail    // [16; 17; 18; 19; 20; 21; 22]
```

### item (Array, List, Seq)

Gets the element at a given index.

```fsharp
let input = [1..7]

input |> List.item 5      // 6
input |> List.item 8      // ArgumentException
```

### take (Array, List, Seq)

Returns the elements of the collection up to a specified count.

```fsharp
let input = [1..10]

input |> List.take 5        // [1; 2; 3; 4; 5]
input |> List.take 11       // InvalidOperationException
```

### truncate (Array, List, Seq)

Returns a collection that when enumerated returns at most N elements.

```fsharp
let input = [1..10]

input |> List.truncate 5    // [1; 2; 3; 4; 5]
input |> List.truncate 11   // [1; 2; 3; 4; 5; 6; 7; 8; 9; 10]
```

### takeWhile (Array, List, Seq)

*takeWhile* returns each item in a new collection until it reaches an item that
does not meet the predicate.

```fsharp
let input = [1..10]

input |> List.takeWhile (fun x -> x / 7 = 0)   // [1; 2; 3; 4; 5; 6]
input |> List.takeWhile (fun x -> x / 17 = 0)     // [1; 2; 3; 4; 5; 6; 7; 8; 9; 10]
```

### skip (Array, List, Seq)

Returns a collection that skips N elements of the underlying sequence and then yields
the remaining elements.

```fsharp
let input = [ for i in 1 .. 10 -> i * i ]  // [1; 4; 9; 16; 25; 36; 49; 64; 81; 100]

input |> List.skip 5                       // [36; 49; 64; 81; 100]
input |> List.skip 11                      // ArgumentException
```

### skipWhile (Array, List, Seq)

Returns a collection, when iterated, skips elements of the underlying array (list, seq)
while the given predicate returns true, and then yields the remaining elements.

```fsharp
let mySeq = seq { for i in 1 .. 10 -> i * i }    // 1 4 9 16 25 36 49 64 81 100

mySeq |> Seq.skipWhile (fun elem -> elem < 10)   // 16 25 36 49 64 81 100
mySeq |> Seq.skipWhile (fun elem -> elem < 101)  // Empty seq
```

### exists (Array, List, Seq)

Tests if any element of the collection satisfies the given predicate.

```fsharp
let inputs = [0..3]

inputs |> List.exists (fun elem -> elem = 3)      // true
inputs |> List.exists (fun elem -> elem = 10)     // false
```

### exists2 (Array, List, Seq)

Tests if any pair of corresponding elements of the collections satisfies
the given predicate. The collections must have equal lengths, except for Seq where extra elements are ignored.

```fsharp
let list1to5 = [1 .. 5]           // [1; 2; 3; 4; 5]
let list0to4 = [0 .. 4]           // [0; 1; 2; 3; 4]
let list5to1 = [5 .. -1 .. 1]     // [5; 4; 3; 2; 1]
let list6to1 = [6 .. -1 .. 1]     // [6; 5; 4; 3; 2; 1]

(list1to5, list5to1) ||> List.exists2 (fun i1 i2 -> i1 = i2)    // true
(list1to5, list0to4) ||> List.exists2 (fun i1 i2 -> i1 = i2)    // false
(list1to5, list6to1) ||> List.exists2 (fun i1 i2 -> i1 = i2)    // ArgumentException
```

### forall (Array, List, Seq)

Tests if all elements of the collection satisfy the given predicate.

```fsharp
let inputs = [2; 4; 6; 8; 10]

inputs |> List.forall (fun i -> i % 2 = 0)    // true
inputs |> List.forall (fun i -> i % 2 = 0)    // false
```

### forall2 (Array, List, Seq)

Returns true if all corresponding elements of the collection satisfy the given predicate pairwise.
The collections must have equal lengths, except for Seq where extra elements are ignored.

```fsharp
let lst1 = [0; 1; 2]
let lst2 = [0; 1; 2]
let lst3 = [2; 1; 0]
let lst4 = [0; 1; 2; 3]

(lst1, lst2) ||> List.forall2 (fun i1 i2 -> i1 = i2)    // true
(lst1, lst3) ||> List.forall2 (fun i1 i2 -> i1 = i2)    // false
(lst1, lst4) ||> List.forall2 (fun i1 i2 -> i1 = i2)    // ArgumentException
```

### contains (Array, List, Seq)

Returns true if a collection contains an equal value:

```fsharp
let rushSet = ["Dirk"; "Lerxst"; "Pratt"]
let gotSet = rushSet |> List.contains "Lerxst"      // true
```

### filter, where (Array, List, Seq)

Returns a new collection containing only the elements of the collection
for which the given predicate returns true.

```fsharp
let data =
    [("Cats",4)
     ("Tiger",5)
     ("Mice",3)
     ("Elephants",2) ]

// [("Cats", 4); ("Mice", 3)]
data |> List.filter (fun (nm, x) -> nm.Length <= 4)
data |> List.where (fun (nm, x) -> nm.Length <= 4)
```

### length (Array, List, Seq)

Returns the length of the collection.

```fsharp
[ 1 .. 100 ] |> List.length         // 100
[ ] |> List.length                  // 0
[ 1 .. 2 .. 100 ] |> List.length    // 50
```

### distinctBy (Array, List, Seq)

Returns a collection that contains no duplicate entries according to the generic hash and
equality comparisons on the keys returned by the given key-generating function.

```fsharp
let inputs = [-5 .. 10]

// [-5; -4; -3; -2; -1; 0; 6; 7; 8; 9; 10]
inputs  |> List.distinctBy (fun i -> abs i)
```

### distinct (Array, List, Seq)    == Distinct() in LINQ

Returns a collection that contains no duplicate entries according to generic hash and
equality comparisons on the entries.

```fsharp
[1; 3; 9; 4; 3; 1] |> List.distinct    // [1; 3; 9; 4]
[1; 1; 1; 1; 1; 1] |> List.distinct     // [1]
[ ] |> List.distinct                    // error FS0030: Value restriction
```

### sortBy (Array, List, Seq)    == OrderBy() in LINQ

Sorts the given collection using keys given by the given projection.
Keys are compared using *Operators.compare*.

```fsharp
[1; 4; 8; -2; 5] |> List.sortBy (fun x -> abs x)    // [1; -2; 4; 5; 8]
```

### sort (Array, List, Seq)

Sorts the given list using *Operators.compare*.

```fsharp
[1; 4; 8; -2; 5] |> List.sort    // [-2; 1; 4; 5; 8]
```

### sortByDescending (Array, List, Seq)

```fsharp
[-3..3] |> List.sortByDescending (fun x -> abs x)   // [-3; 3; -2; 2; -1; 1; 0]
```

### sortDescending (Array, List, Seq)

```fsharp
[0..5] |> List.sortDescending    // [5; 4; 3; 2; 1; 0]
```

### sortWith (Array, List, Seq)

Sorts the given collection using the given comparison function.

```fsharp
let lst = ["<>"; "&"; "&&"; "&&&"; "<"; ">"; "|"; "||"; "|||"]

let sortFunction (str1: string) (str2: string) =
    if (str1.Length > str2.Length) then
        1
    else
        -1

// ["|"; ">"; "<"; "&"; "||"; "&&"; "<>"; "|||"; "&&&"]
lst |> List.sortWith sortFunction
```

<a name="ArrayListAndSeqFunctions"></a>Array, List and Seq functions
-------------------

### allPairs (Array, List, Seq)

Takes 2 arrays (list, seq), and returns all possible pairs of elements.

```fsharp
let arr1 = [| 0; 1 |]
let arr2 = [| 4; 5 |]

(arr1, arr2) ||> Array.allPairs arr1 arr2    // [|(0, 4); (0, 5); (1, 4); (1, 5)|]
```

### append (Array, List, Seq)

Combines 2 arrays (list, seq).

```fsharp
let list1 = [33; 5; 16]
let list2 = [42; 23; 18]

List.append list1 list2     // [33; 5; 16; 42; 23; 18]
```

### averageBy (Array, List, Seq)

*averageBy* take a function as a parameter, and this function's results are used
to calculate the values for the average.

```fsharp
// val avg1 : float = 2.0
[1..3] |> List.averageBy (fun elem -> float elem)

// val avg2 : float = 4.666666667
[| 1..3 |] |> Array.averageBy (fun elem -> float (elem * elem))
```

### Seq.cache

*Seq.cache* creates a stored version of a sequence. Use *Seq.cache* to avoid reevaluation
of a sequence, or when you have multiple threads that use a sequence,
but you must make sure that each element is acted upon only one time.
When you have a sequence that is being used by multiple threads,
you can have one thread that enumerates and computes the values for the original sequence,
and remaining threads can use the cached sequence.

### choose (Array, List, Seq)

*choose* enables you to transform and select elements at the same time.

```fsharp
let list1 = [33; 5; 16]

// [34; 6; 17]
list1 |> List.choose (fun elem -> Some(elem + 1)) 
```

### compareWith (Array, List, Seq)

Compare two arrays (lists, seq) by using the *compareWith* function.
The function compares successive elements in turn, and stops when it encounters
the first unequal pair. Any additional elements do not contribute to the comparison.

```fsharp
let sq1 = seq { 1; 2; 4; 5; 7 }
let sq2 = seq { 1; 2; 3; 5; 8 }
let sq3 = seq { 1; 3; 3; 5; 2 }

let compareSeq seq1 seq2 =
    (seq1, seq2 ||> Seq.compareWith (fun e1 e2 ->
        if e1 > e2 then 1
        elif e1 < e2 then -1
        else 0)

let compareResult1 = compareSeq sq1 sq2     // int = 1
let compareResult2 = compareSeq sq2 sq3     // int = -1
```

### concat (Array, List, Seq)

*concat* is used to join any number of arrays (lists, seq).

```fsharp
// int list = [1; 2; 3; 4; 5; 6; 7; 8; 9]
List.concat [[1; 2; 3]; [4; 5; 6]; [7; 8; 9]]
```

### Array.copy

Creates a new array that contains elements that are copied from an existing array.
**The copy is a shallow copy**, which means that if the element type is a reference type,
only the reference is copied, not the underlying object. 

```fsharp
let firstArray : StringBuilder array = Array.init 3 (fun index -> new StringBuilder(""))
let secondArray = Array.copy firstArray
// Two arrays: [|; ; |]

firstArray.[0] <- new StringBuilder("Test1")
// firstArray: [|Test1; ; |]
// secondArray: [|; ; |]

firstArray.[1].Insert(0, "Test2") |> ignore
// firstArray: [|Test1; Test2; |]
// secondArray: [|; Test2; |]
```

<a name="Acknowledgments"></a>Acknowledgments
--------

Thanks goes to these people/projects:

- [dungpa/fsharp-cheatsheet](https://github.com/dungpa/fsharp-cheatsheet)
- [artag/fsharp-cheatsheet](https://github.com/artag/fsharp-cheatsheet)
- [thriuin/fsharp-cheatsheet](https://github.com/thriuin/fsharp-cheatsheet)

