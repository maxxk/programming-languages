---
layout: default
---

# Project Step 1 Example

## Design document

The proposed language is a simplified version of REFAL programming language extended by static typing.
The language is oriented towards list rewriting. It features a pattern-matching facility and recursive functions.

Core data structure of the language is list with associative concatenation constructor. Statements of the language are side-effect free. 

The following differences distinguish the language from Refal-2:

- no support for structural brackets (so we can't define trees);
- each pattern may have at most one e-variable (open expression);
- static "algorithmic" type system;
- built-in integers supporting negative numbers.

It supports the following data types:
- integral number;
- symbol (some name or identifier).


The classical Palindrome example may be translated from Refal-2 to the target language:

```
Bool : Type {
    True = ;
    False = ;
}

 Pal : e.1 -> Bool { 
    = True;
    s.1 = True;
    s.1 e.2 s.1 = <Pal e.2>;
    e.1 = False;  
}
```

Function definition consists of funcion name, function type and a sequence of statements.
Each statement includes left part and right part. Left part is a pattern. Patterns may include constants and variables. Variables are denoted with variable type and variable name. Right part is a result, it may contain a (possibly recursive) call. Callee name with call arguments are enclosed in angular brackets.

Variable types:

- `s.var-name` : a single datum (an integer or a symbol)
- `i.var-name` : a single integer (1, -10, 50, 1000)
- `w.var-name` : a single symbol 
- `e.var-name` : a sequence (possibly empty) of data

Function evaluation:
(see Refal documentation, in short: arguments are matched with statements pattern sequentially, the result of the first matched statement is the result of the function, calls in result are expanded after the return)

Each function has a type. Type may be defined as a special function (of type `Type`) accepting some subset of values. Normal function type is declared in the following way:

```
<type of argument> -> <type of result>
```

The compiler may check types statically, rejecting the programs for which it can't prove type safety.