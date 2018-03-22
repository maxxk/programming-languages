# Software and Programming Language Theory
## Static and dynamic semantics
## Scope
## Typing

<style>
.twocolumn {
  -moz-column-count: 2;
  -webkit-column-count: 2;
  column-count: 2;
}
.twocolumn div.sourceCode {
  overflow-x: visible;
}
.smallish pre {
  margin: 5px auto;
}
.small { font-size: small !important; }
.smallish { font-size: 0.7em !important; }
.smaller { font-size: 0.8em !important; }
.large { font-size: 1.5em !important; }
.huge { font-size: 2em !important; }
</style>

https://maxxk.github.io/programming-languages/

maxim.krivchikov@gmail.com

# Programming language specification
```{.graphviz .dot}
digraph Spec {
  edge [minlen=3.0];

  { rank=same; Syntax, Semantics, Pragmatics };
  Specification -> Syntax;
  Specification -> Semantics;
  Specification -> Pragmatics;
  Semantics -> "Static Semantics";
  "Static Semantics" -> "Name binding";
  "Static Semantics" -> Typing;
  "Name binding" -> Typing [constraint=false];
  Semantics -> "Dynamic Semantics";
  Syntax -> Grammar [label="Specification"];
  Syntax -> "Parse Tree" [label="Proof"];
  Syntax -> AST [label="Corollary"];  
}
```

# Programming language semantics

Static semantics
~ static properties of programs, which can be determined without executing them
~ variable bindings (what does variable name correspond to)
~ static type checking (do the expressions satisfy constraints of typing system)
~ "context-sensitive" restriction on valid programs

Dynamic semantics
~ how do we compute the value of expressions, statements, etc.
~ "recursively enumerable" restriction on valid programs

# Naming
In programming languages we usually name entities

- variables
- function arguments
- classes
- modules

# Binding
Binding is the process of assigning the meaning to a name.
<div class="twocolumn smaller">
```java
public class SimpleDemoClass  
{            
     public void GetData<T>(T obj)  
     {  
        Console.WriteLine("INSIDE GetData<T>,"+ obj.GetType().Name);  
     }        
     public void GetData(int x)  
     {  
        Console.WriteLine("INSIDE GetData" + x.GetType().Name);  
     }        
     public void GetxNextData<T>(T obj)  
     {  
         GetData(obj);  
     }      
 }  
```

```java
class Program  
{  
      Static void Main(string[] args)  
      {    
           SimpleDemoClass sobj = new SimpleDemoClass();  
           sobj.GetData("data is for testing by-Devesh");  
           sobj.GetData(95);  
           sobj.GetxNextData(1234);  
           Console.ReadKey();  
       }  
}  
```
</div>
<div class="small">Example from: http://www.c-sharpcorner.com/UploadFile/deveshomar/generic-method-overloading-in-C-Sharp/</div>

# Scope
*Scope* of a binding is a textual region in program in which a binding is active. We may also use term *scope* to call the region of a program of maximal size in which no bindings are destroyed (masked).
```c
char *a = "QWE123\0";
char* fun() {
  double a = 0;
  return a;
}
```

*Lexical scope* (implemented in most of statically-typed programming languages, e.g. C/C++) binds identifiers inside some specific parts of program source code (lexical context).


# Closure

A closure is a persistent scope which holds on to local variables even after the code execution has moved out of that block [<a href="https://stackoverflow.com/a/7464475">1</a>].

<div class="twocolumn smaller">
```javascript
function external() {
  var a = 0;
  function f() {
    a += 1;
    return a;
  }
  function g() {
    a -= 1;
    return a;
  }
  return [f, g];
}
```

```c
typedef char (*fooType)();
fooType fun() {
  char a = 0;
  char f2() {
    a += 1;
    return a;
  }
  return f2;
}

int main(void) {
  printf("Result: %d", fun()());
  return 0;
}

```
</div>

Static semantics: which variables to persist and share between closures? 

# Dynamic scope
<div class="twocolumn">
Some languages (like ECMAScript/JavaScript or some kinds of LISP) have a concept of the *dynamic scoping*. Binding of identifiers occurs at run-time.
```javascript
var a = 0;
function f() {
  var b = a;
  with ({a: 1}) {
    b += a;
  }
  return b; // b = 1
}
```

`this` in object-oriented language may be seen as either an implicit argument to a function or as a way of introducing a dynamic scope.

```javascript
function g() {
  return this.x;
}
var A = { x: 0, g }
var B = { x: 1, g }
A.g() // returns 0
B.g() // returns 1
g() // returns 2
```
</div>

# Name overloading
<div class="twocolumn smallish">
```c++
template<typename T>
T mul(int i, int j)
{
   // If you get a compile error, it's because you did not use
   // one of the authorized template specializations
   const int k = 25 ; k = 36 ;
}
template<>
int mul<int>(int i, int j)
{
   return i * j ;
}

template<>
std::string mul<std::string>(int i, int j)
{
   return std::string(j, static_cast<char>(i)) ;
}
```

If the language supports name overloading, the same identifier in the same place may have a different meaning depending on some external information (like typing). Usually in imperative languages name overloading is restricted to functions names.
```c++
int mul(int i, int j) { return i*j; }
std::string mul(char c, int n) { return std::string(n, c); }

int n = mul(6, 3); // n = 18
std::string s = mul(static_cast<char>(6), 2); // s = "110"
int n = mul<int>(6, 3); // n = 18
std::string s = mul<std::string>(54, 2); // s = "110110"
short n2 = mul<short>(6, 3); // error: assignment of read-only variable ‘k’
```

Languages supporting function overloading: C++, C#

Languages without support for function overloading: Haskell, ECMAScript, C (but C11 has the `_Generic` keyword: http://stackoverflow.com/a/25026358)
</div>
<div class="small">Example from: http://stackoverflow.com/questions/226144/overload-a-c-function-according-to-the-return-value</div>

# Lexical scope declarative region

Where does declaration effect start/stop?

<div class="twocolumn">

```python 
def f(x):
    x = 0
    for x in [1,2,3]:
        print x
    return x # return 3
```

```javascript
function f(x) {
    // let x = 0;  // "Syntax error", 
                   // the same region 
                   // as function arguments
    x = 0;
    for (let x of [1,2,3]) {
      console.log(x);
    }
    return x; // return 0
}
```
</div>

Use before definition

<div class="twocolumn">

```c
int f() {
    // warning: implicit declaration ...
    return g();
}

int g() {
    return 0;
}
```

```rust
fn f() -> u32 {
    return g()
}

fn g() -> u32 {
    return 0;
}
```
</div>


# Static semantics representation
Let us return to the topic of formal mechanized analysis of programs.
```{.graphviz .dot}
digraph  G {
  graph [rankdir="LR"];
  String -> AST [label="Syntax"];
  AST -> "AST+Bindings" [label="Static semantics"]
}
```
Syntax is a transformation from `String` to <abbr title="Abstract Syntax Tree">`AST`</abbr>. Static semantics, in the same way, is a transformation from AST to AST with variable bindings information. How would we store this information?

Easier approach: generate unique names and store scope in parallel.

AST+Bindings = Σ (ast : AST /[Id := ℕ]) . (scope :  Map<ℕ, AST> ) . (p : ∀ { id : ℕ } ∈ ast, id ∈ scope).

<div class="small">Example at: Vasenin V. A., Krivchikov M. A. Ecma-335 static formal semantics // Programming and Computer Software. — 2012. — Vol. 38, no. 4. — P. 183–188. http://dx.doi.org/10.1134/S0361768812040056</div>

# Higher order abstract syntax
— the technique to capture the variable binding in abstract syntax tree (Miller, 1987; Phenning, 1988).
<div class="twocolumn">
Abstract syntax:
```ocaml
type var = string
type typ =
  | Bool
  | Arrow of typ * typ
type exp =
  | Var of var
  | True
  | False
  | App of exp * exp
  | Abs of var * exp
```
Higher-order syntax captures the name binding:
<div>
```ocaml
type exp =
  | True
  | False
  | App of exp * exp
  | Abs of exp -> exp
```
</div>
Parametrized Higher-order syntax captures the types of variables:
```ocaml

type ('t, 'V) exp =
    | Var : 't 'V -> ('t, 'V) exp
    | True : (bool, 'V) exp
    | False : (bool, 'V) exp
    | App : ('d -> 'r, 'V) exp * ('d, 'V) exp -> ('r, 'V) exp
    | Abs : ('d 'V -> ('r, 'V) exp) -> ('d -> 'r, 'V) exp
```

http://adam.chlipala.net/papers/PhoasICFP08/
</div>

# Ornaments
Conor McBride. «Ornamental Algebras, Algebraic Ornaments»

One base data type defines the induction structure, basic constructors:
```agda
data Nat where
  0 : Nat
  succ : Nat → Nat
```

Then we place an additional data (ornament) to the leaves of the induction structure:
```agda
data List [A : Set] from Nat where
   List_A ← nil
    | cons [a : A] (as : List_A)

data Vec [A : Set] from Nat where
  Vec_A n ← nil [q : n ≡ 0]
     | cons [n' : Nat][ q : n = succ n' ] (a : A) (vs : Vec_A n)
```


<div class="small">
https://personal.cis.strath.ac.uk/conor.mcbride/pub/OAAO/LitOrn.pdf
http://arxiv.org/pdf/1201.4801.pdf
</div>


# Recursion

```java
class A { B b; }
class B { A a; }
```
Suppose we started translating <abbr title="Abstract Syntax Tree">AST</abbr> to static semantics. Before we process second line and see that `B` is a class, what is the static semantics of `B` at the first line?

Approach: fixed point (works for classical Domain Theory, doesn't work well in a constructive setting).

N.S. Papaspyrou. Formal semantics of the C Programming Language (PhD thesis).

# Homework assignments
**Task 5.1.** ** Implement a translator from simply-typed lambda calculus <abbr title="Abstract Syntax Tree">AST</abbr> to <abbr title="Higher-Order Abstract Syntax">HOAS</abbr> in Haskell or any other language of your choice (except OCaml) which is expressive enough to represent HOAS.

**Task 5.2.** *** Implement a translator from simply-typed lambda calculus <abbr title="Abstract Syntax Tree">AST</abbr> to <abbr title="Parametrized Higher-Order Abstract Syntax">PHOAS</abbr> in Haskell or any other language of your choice (except the original OCaml) which is expressive enough to represent PHOAS.

**Task 5.3.** \* Look at the assembly code generated by GCC on example code from "Closure" slide and explain how "closures" are implemented in this case.

**Task 5.4.** Describe static semantics of BNF in terms of Parametrized Higher Order Syntax.

a. "on paper" (\*\*)
b. in Coq, following the original paper (\*\*\*)

# Project
**Project Step 3.** Implement a static formal semantics for your programming language (in Agda or Coq). Choose one of the following approaches.
- **Project Step 3a.** *  Assign the unique identifiers (e.g. natural numbers) to each bindable identifier leaf in the AST and create the mapping between the unique identifiers and static semantics subtrees.
- **Project Step 3b.** ** Use Higher-Order Abstract Syntax.
- **Project Step 3c.** *** Use Parametrized Higher-Order Abstract Syntax
