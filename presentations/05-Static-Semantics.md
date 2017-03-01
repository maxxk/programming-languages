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

Course page: https://maxxk.github.io/programming-languages-2016/
Contact author: maxim.krivchikov@gmail.com

# Programming language specification
```{.graphviz .dot}
digraph Spec {
  edge [minlen=3.0];

  { rank=same; Syntax, Semantics, Pragmatics };
  Specification -> Syntax;
  Specification -> Semantics;
  Specification -> Pragmatics;
  Syntax -> Grammar [label="Specification"];
  Syntax -> "Parse Tree" [label="Proof"];
  Syntax -> AST [label="Corollary"];  
}
```

# Syntax (concluding remarks)
BNF has a graph structure (actually, a directed hypergraph with per-edge ordered destination nodes). Parse tree is a tree derived from the graph (when you encounter a cycle, vertex is duplicated).
```{.graphviz .dot}
digraph Syntax {
  { rank=same; term, factor, atom };
  term -> factor;
  term -> seq_term;
  seq_term -> term [label="1"];
  seq_term -> "'+'" [label="2"];
  seq_term -> factor [label="3"];

  factor -> atom;
  factor -> seq_factor;
  seq_factor -> factor [label="1"];
  seq_factor -> "'*'" [label="2"];
  seq_factor -> atom [label="3"];

  atom -> number;
  atom -> seq_atom;
  seq_atom -> "(" [label="1"];
  seq_atom -> term [label="2"];
  seq_atom ->  ")" [label="3"];
}
```

<div class="smallish">
```c
term ::= factor | term '+' factor
factor ::= atom | factor '*' atom
atom ::= number | '(' term ')'
```
```c
(3+2)*4+1
```
</div>

# Mechanization
<div class="smaller">
Programs are complex. In theory, you can make a mathematical description of a programming language with some assumptions. You can't usually prove properties on paper. Therefore we have to use some (mathematically correct) mechanization tools to make a precise reasoning about  the program. In the present course we employ Agda and Coq as the mechanization tools.
</div>

<div class="twocolumn smallish">
```c++
class HashEntry
{
private:

      int key;
      int value;

public:

      HashEntry(int key, int value)
      {
            this->key = key;
            this->value = value;
      }

      int getKey() { return key; }

      int getValue() { return value; }
};

const int TABLE_SIZE = 128;

class HashMap
{
private:

      HashEntry **table;

public:

      HashMap()
      {
            table = new HashEntry*[TABLE_SIZE];

            for (int i = 0; i < TABLE_SIZE; i++)
                  table[i] = NULL;
      }

      int get(int key)
      {
            int hash = (key % TABLE_SIZE);

            while (table[hash] != NULL && table[hash]->getKey() != key)
                  hash = (hash + 1) % TABLE_SIZE;

            if (table[hash] == NULL)
                  return -1;
            else
                  return table[hash]->getValue();
      }

      void put(int key, int value)
      {
            int hash = (key % TABLE_SIZE);

            while (table[hash] != NULL && table[hash]->getKey() != key)
                  hash = (hash + 1) % TABLE_SIZE;

            if (table[hash] != NULL)
                  delete table[hash];

            table[hash] = new HashEntry(key, value);
      }     

      ~HashMap()
      {
            for (int i = 0; i < TABLE_SIZE; i++)
                  if (table[i] != NULL)
                        delete table[i];

            delete[] table;
      }
};
```
2009 nodes in an abstract syntax tree.
![](images/S6kW9.jpg)
</div>
<div class="small">Example from: http://stackoverflow.com/a/11025084</div>

# Programming language semantics

Static semantics
~ static properties of programs, which can be determined without executing them
~ variable bindings (what does variable name correspond to)
~ static type checking (do the expressions satisfy constraints of typing system)

Dynamic semantics
~ how do we compute the value of expressions, statements, etc.

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
  dobule a = 0;
  return a;
}
```

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
Languages not supporting function overloading: Haskell, ECMAScript, C (but C11 has the `_Generic` keyword: http://stackoverflow.com/a/25026358)
</div>
<div class="small">Example from: http://stackoverflow.com/questions/226144/overload-a-c-function-according-to-the-return-value</div>

# Static semantics representation {.smaller}
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
AST+Bindings = Σ (ast : AST /[Id := ℕ]) . (scope :  Map<ℕ, AST> ) . (p : ∀ id : ℕ ∈ ast, id ∈ scope).
<div class="small">Example at: Vasenin V. A., Krivchikov M. A. Ecma-335 static formal semantics // Programming and Computer Software. — 2012. — Vol. 38, no. 4. — P. 183–188. http://dx.doi.org/10.1134/S0361768812040056</div>

# Higher order abstract syntax {.smaller}
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
type 't var
type 't exp =
    | Var : 't var -> 't exp
    | True : bool exp
    | False : bool exp
    | App : ('d -> 'r) exp * 'd exp -> 'r exp
    | Abs : ('d var -> 'r exp) -> ('d -> 'r) exp
```

http://adam.chlipala.net/papers/PhoasICFP08/

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
**Task 5.1.** ** Implement a translator from simply-typed lambda calculus <abbr title="Abstract Syntax Tree">AST</abbr> to <abbr title="Higher-Order Abstract Syntax">HOAS</abbr> in Haskell or any other language of your choice (except the original OCaml) which is expressive enough to represent HOAS.

**Task 5.2.** *** Implement a translator from simply-typed lambda calculus <abbr title="Abstract Syntax Tree">AST</abbr> to <abbr title="Parametrized Higher-Order Abstract Syntax">PHOAS</abbr> in Haskell or any other language of your choice (except the original OCaml) which is expressive enough to represent PHOAS.

# Project
**Project Step 4.** Implement a static formal semantics for your programming language (in Agda or Coq). Choose one of the following approaches.
- **Project Step 4a.** *  Assign the unique identifiers (e.g. natural numbers) to each bindable identifier leaf in the AST and create the mapping between the unique identifiers and static semantics subtrees.
- **Project Step 4b.** ** Use Higher-Order Abstract Syntax.
