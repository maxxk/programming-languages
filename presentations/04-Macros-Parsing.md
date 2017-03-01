# Software and Programming Language Theory
## Project
## Formal syntax analysis
## Macros as a part of syntax

<style>
.twocolumn {
  -moz-column-count: 2;
  -webkit-column-count: 2;
}
.small { font-size: small !important; }
.smaller { font-size: 0.8em !important; }
.large { font-size: 1.5em !important; }
.huge { font-size: 2em !important; }
</style>

Course page: https://maxxk.github.io/programming-languages-2016/
Contact author: maxim.krivchikov@gmail.com

# Project
Let's write a verifiable implementation of a toy programming language. In total you will get at least 10 stars.

**Task 1.1*** _(addition)_ Coq, Agda; Idris (if you feel adventurous).

**Project Step 1.*** Write a "design document" (short informal description, 1-4 pages, in English) of a toy programming language of your choice. Design document must include the following information:
- what is the main focus of the language
- examples of language statements and results of evaluation for such statements

Language must support variables or named function arguments.

# Project ideas
- (simple) Imp (simple imperative programming language)
- (simple) Simply-typed lambda calculus with natural number operations (simple functional programming language)
- (simple) mathematical expression calculator with variables, elementary functions and symbolic differentiation
- (medium) Lisp-ish (dynamic functional, simple syntax)
- (medium) Smalltalk-ish (dynamic object-oriented)
- (medium) SQL-like declarative language
- (hard) Refal-ish (pattern matching-based, like Markov algorithms), or, easier, regex engine or some parser generator
- any nontrivial domain-specific language

# Proof assistants: Coq and Agda
Coq and Agda are the proof assistants based on dependently-typed lambda calculus ([remember the previous semester](https://maxxk.github.io/formal-models-2015/)).

## Coq
ML-like syntax.
Homepage: https://coq.inria.fr
Textbook: http://adam.chlipala.net/cpdt/
Quickstart (in Russian): https://habrahabr.ru/post/182442/
Another short texbook (in Russian): http://lpcs.math.msu.su/~krupski/download/coq_pract.pdf

## Agda
Haskell-like syntax.
Homepage: http://wiki.portal.chalmers.se/agda/pmwiki.php?n=Main.HomePage
Introduction paper: http://www.cse.chalmers.se/~ulfn/papers/afp08/tutorial.pdf
Example (in Russian): https://habrahabr.ru/post/148769/

# Coq and Agda
In Coq and Agda all definable functions are (under the normal circumstances) total, which means:
1. The function will accept any input of the specified type, errors like Haskell's "non-exhaustive patterns" are not possible:

```haskell
helper :: Integer -> [Integer] -> [Integer] -> [(Integer,Integer)] -> [Integer]
helper n [] (v) _ = []
helper n (x:y:xs) (v) (c:cs) =
    if (chinese3 (b:c:cs) == n)
      then (x:v)
    else helper n (xs) (x:v) ((n `mod` y,y):c:cs)
    where b =(n `mod` x,x)

-- helper 10 primes [] [] ⟶ error: Non-exhaustive patterns in function helper.
```

2. The function will terminate for any input, infinite recursion is not allowed:
```haskell
helper a b c d = helper a (a:b) c d
```

# Agda examples
Inductive datatype is defined by a set of constructors. Note that indentation is significant (like in Python and Haskell)
```agda
data Nat : Set where
    zero : Nat
    succ : Nat → Nat
```
Function on inductive types are defined by the means of pattern matching.
```agda
plus : Nat → Nat → Nat
plus zero m = m
plus (succ n) m = succ (plus n m)
```

# Agda examples
Dependent product indexed type familiy. Note that the implicit argument n to `cons` is enclosed in braces.
```agda
data Vec (A : Set) : Nat → Set where
    [] : Vec A zero
    cons : {n : Nat} → A → Vec A n → Vec A (succ n)

cons zero [] -- Vector with a single element (0)

head : {A : Set}{n : Nat} → Vec A (succ n) → A
head (cons x xs) = x
```

# Coq examples
Inductive data types. Indentation is not significant in Coq. Statements are terminated by dot `.`
```coq
Inductive nat : Type :=
 | O : nat
 | S : nat → nat.
```
There are some different keywords for definitions (`Definition`, `Inductive`, `Fixpoint`, `Program Definition`). Functions are defined like in lambda-calculus, pattern matching is possible with `match` expression:
```coq
Fixpoint plus (a b : nat) : nat :=
  match a with
  | O ⇒ b
  | S a' ⇒ S (plus a' b)
  end.
```

# Coq examples
Dependent type family. As in Agda, implicit arguments are written inside braces, explicit dependent arguments are written insied the round brackets.
```coq
Inductive Vector (A : Set) : nat → Set :=
  | Nil : Vector A O
  | Cons : forall {n : Nat}, A → Vector A n → Vector a (S n).
```

```coq
Definition head' A n (vec : Vector A n) :=
  match vec in (Vector A n) return
    (match n with O ⇒ unit | S _ ⇒ A end) with
    | Nil ⇒ tt
    | Cons h _ ⇒ h
  end.

Definition head A n (vec : Vector A (S n)) : A := head' vec.
```

# Formal syntax analysis
Parser is a function which implements syntax analysis.
Suppose we have defined some language grammar G (represented as AST) and implemented a parser P.

P : String → G?

How can we make sure that our parser will:
1. accept all strings from G-defined language.
2. not accept any string outside of G-defined language.
3. will terminate for every finite input.

# Couldn't we just write a parser in Agda?
From the previous class: left recursion.
```
term ::= factor | term '+' factor
factor ::= atom | factor '*' atom
atom ::= number | '(' term ')'
```

```
x+1 ⟶ ⟂
```
(try to parse a `term`, `x` is not a `factor` so it must be a `term` and now we have a cycle)


# Danielsson. Total Parser Combinators (2010)
Main idea is: use the lazy computation. Total, dependently-typed programming languages may represent infinite lazy computations as a corecursive type. After the parsing process is represented as tree with some infinite-depth paths, make a breadth-first search of successful parse result.
```agda
data List (A : Set) : Set where
  [] : List A
  _::_ : A → List A → List A

data Colist (A : Set) : Set where
  [] : Colist A
  _::_ : A → ∞ (Colist A) → Colist A

♯ : {A : Set} →  A → ∞ A
♭ : {A : Set} → ∞ A→ A
```

<span class="smaller">
Danielsson N.A. Total parser combinators // Proceedings of the 15th ACM SIGPLAN international conference on Functional programming - ICFP ’10. ACM Press, 2010. P. 285–285.
Source and paper is available at: http://www.cse.chalmers.se/~nad/publications/danielsson-parser-combinators.html
</span>

# Danielsson. Total Parser Combinators (2010)
```agda
map : ∀{A B} → ( A → B) → Colist A → Colist B
map f [ ] = [ ]
map f (x::xs) = f x :: ♯ map f (♭xs)
```

```agda
mutual
  -- The index is true if the corresponding language contains the
  -- empty string (is nullable).
  data P : Bool → Set where
    fail  : P false
    empty : P true
    tok   : Tok → P false
    _∣_   : ∀ {n₁ n₂} → P n₁ →            P n₂ → P (n₁ ∨ n₂)
    _·_   : ∀ {n₁ n₂} → P n₁ → ∞⟨ not n₁ ⟩P n₂ → P (n₁ ∧ n₂)

  -- Coinductive if the index is true.
  ∞⟨_⟩P : Bool → Bool → Set
  ∞⟨ true  ⟩P n = ∞ (P n)
  ∞⟨ false ⟩P n =    P n
```

# Danielsson. Total Parser Combinators (2010)
Example from: http://www.cse.chalmers.se/~nad/publications/danielsson-parser-combinators/TotalParserCombinators.Examples.Expression.html#233

```agda
module Monadic where
  mutual
    term   = factor
           ∣ ♯ term            >>= λ e₁ →
             tok '+'           >>= λ _  →
             factor            >>= λ e₂ →
             return (e₁ + e₂)
    factor = atom
           ∣ ♯ factor          >>= λ e₁ →
             tok '*'           >>= λ _  →
             atom              >>= λ e₂ →
             return (e₁ * e₂)
    atom   = number
           ∣ tok '('           >>= λ _  →
             ♯ term            >>= λ e  →
             tok ')'           >>= λ _  →
             return e
```

# And what about proofs?
Simplification
```agda
-- f <$> fail                  → fail
-- f <$> return x              → return (f x)
-- fail         ∣ p            → p
-- p            ∣ fail         → p
-- token >>= p₁ ∣ token >>= p₂ → token >>= (λ t → p₁ t ∣ p₂ t)  (*)
-- ...
simplify₁ : ∀ {Tok R xs} (p : Parser Tok R xs) →
            ∃₂ λ xs (p′ : Parser Tok R xs) → p ≅P p′
```
Completeness
```agda
-- A proof showing that all functions of type List Bool → List R can
-- be realised using parser combinators (for any R, assuming that bag
-- equality is used for the lists of results).
parser⇒fun : ∀ {R xs} (p : Parser Bool R xs) {x s} →
             x ∈ p · s ⇿ x ∈ parse p s
maximally-expressive :
 ∀ {R} (f : List Bool → List R) {s} →
 parse (grammar f) s ≈[ bag ] f s
```

# Koprowski, Binsztok. TRX: A Formally Verified Parser Interpreter
Main idea: like in original PEG parsers, disallow direct and indirect left-recursive grammars. [Patented](http://www.google.com/patents/EP2454661A1?cl=en) algorithm :)
```coq
Definition wf_analyse (exp : pexp) (wf : PES.t) : bool :=
  match exp with
  | empty ⇒ true
  | range     ⇒ true
  | terminal a ⇒ true
  | anyChar ⇒ true
  | nonTerminal p ⇒ is_wf (production p) wf
  | seq e1 e2 ⇒ is_wf e1 wf ∧ (if e1 − [gp] → 0 then is_wf e2 wf else true) | choice e1 e2 ⇒ is_wf e1 wf ∧ is_wf e2 wf
  | star e ⇒ is_wf e wf ∧ (negb (e − [gp] → 0))
  |note ⇒is_wf e wf
  | id e ⇒ is_wf e wf
end.
```
<span class="smaller">
Koprowski A., Binsztok H. TRX: A Formally Verified Parser Interpreter // Logical Methods in Computer Science / ed. Gordon A. 2011. Vol. 7, № 2.
Note: some approaches exist to enable left-recursive PEG parsing, I must have references somewhere :)
Medeiros S., Ierusalimschy R. A parsing machine for PEGs // Proceedings of the 2008 symposium on Dynamic languages - DLS ’08. 2008. P. 1–12.
</span>

# TRX: A Formally Verified Parser Interpreter
```coq
Program Fixpoint parse (T : Type) (e : PExp T | is grammar exp e) (s : string)
{measure (e , s ) ≻ } : {r : ParsingResult T | ∃ n , [ e , s ] ⇒ [ n , r ] }
```

# See also
1. Uustalu, Tarmo, Firsov, Denis. Certified Parsing of Regular Languages // Certified Programs and Proofs. Springer International Publishing, 2013. P. 98–113.
2. Jourdan J.-H., Leroy X., Pottier F. Validating LR(1) Parsers // Proceedings of the 21st European Symposium on Programming. 2012. Vol. 7211. P. 397–416.
1. Bernardy, Jean-Philippe, Jansson, Patrik. Certified Context-Free Parsing: a Formalisation of Valiant's Algorithm in Agda: Preprint. Chalmers University of Technology, University of Gothenburg, Sweden, 2016. 27 p.
2. Sjöblom, Thomas Bååth. An Agda proof of the correctness of Valiant’s algorithm for context free parsing: MSc. Göteborg University: Chalmers University of Technilogy, University of Gothenburg, 2013. 63 p.


# Macros
Macro (macroinstruction) — is a rule of generation a set of instructions in compile-time.

Different kinds of macros may be related to both syntax and static semantics of programming language.

# Macroassemblers revisited
Macros are substituted before the assembly time.
<div class="twocolumn">
```x86asm
ForLp           macro   LCV, Start, Stop
ifndef  $$For&LCV&      
$$For&LCV&      =       0
else
$$For&LCV&      =       $$For&LCV& + 1
endif

mov     ax, Start
mov     LCV, ax

MakeLbl $$For&LCV&, %$$For&LCV&

mov     ax, LCV
cmp     ax, Stop
jgDone  $$Next&LCV&, %$$For&LCV&
endm

Next            macro   LCV
inc     LCV
jmpLoop $$For&LCV&, %$$For&LCV&
MakeLbl $$Next&LCV&, %$$For&LCV&
endm
```

```x86asm
ForLp   I, 0, 15
ForLp   J, 0, 6

ldax    A, I, J         ;Fetch A[I][J]
mov     bx, 15          ;Compute 16-I.
sub     bx, I
ldax    b, bx, J, imul  ;Multiply in B[15-I][J].
stax    x, J, I         ;Store to X[J][I]

Next    J
Next    I
```
</div>

# Preprocessors
Preprocessor is a program which operates on input data for some other program. Preprocessors may be used to provide primitive macro facilities.

# C preprocessor
```c
#include <math.h>

#ifndef _WINDOW_H
#define _WINDOW_H
#endif

#define MAX(a,b) ((a) > (b) ? (a) : (b))

#if VERBOSE >= 2
  print("trace message");
#endif

#if !(defined __LP64__ || defined __LLP64__) || defined _WIN32 && !defined _WIN64
#error 32-bit systems not supported
#else
	// we are compiling for a 64-bit system
#endif
```

# C preprocessor
```c
#define xstr(s) str(s)
#define str(s) #s
#define foo 4

str (foo)  // outputs "foo"
xstr (foo) // outputs "4"

#define DECLARE_STRUCT_TYPE(name) typedef struct name##_s name##_t

DECLARE_STRUCT_TYPE(g_object);
// Outputs: typedef struct g_object_s g_object_t;
```
(It is even possible to write limited recursive programs in C preprocessor by itself: https://github.com/pfultz2/Cloak/wiki/Is-the-C-preprocessor-Turing-complete%3F)

# M4 / T4

```csharp
public class Decorator: <#= interface.FullName #>
{    // …
<#
    foreach(Member member in interfaceType.Members)
        WriteMember(member);
#>
}
```
# LISP Reader Macros

# Perl

# Homework assignments

**Task 4.2a*** Write (manually) an extensible parser for LISP-like symbolic expressions (subset of ["R7RS small"](http://www.scheme-reports.org) Scheme specification). Whitespace, identifier and number specifications are omitted as a trivial exercise.
```
<datum> ::= <atom> <optional whitespace> | <list> <optional whitespace>
<atom> ::= <identifier> | <number> | <string>
<string> ::= '"' <string element>* '"'
<string element> ::= <any character except " and \> | '\"' | '\\'
<list> ::= "(" <datum>  ")"
```

# Homework assignments

**Task 4.2b**** Implement reader macros for a subset of a context-free grammar as an interpreter from s-expressions to parser extension.
```scheme
(reader-macro <start-string> <stop-string> <grammar>)
; Example:
(reader-macro "#" "#" (float decimal-digits
    (optional "." (optional decimal-digits))
    (optional "e" (either "+" "-" "") decimal-digits)))

#1.5e11# ⟶ (float "1" ("." ("5")) ("e" ("") "11"))
```

**Task 4.2c*** Write a reader macro for infix arithmetical expressions (addition, multiplication, brackets).

# Project
**Project Step 2.*** Implement an abstract syntax tree type for the language.
Example (I didn't check it in Agda):
```agda
mutual
  data Term where
    TFactor : Factor → Term
    TAdd : Term → Factor → Term
  data Factor where
    FAtom : Atom → Factor
    FMult : Factor → Atom → Factor
  data Atom where
    ANumber : Nat → Atom
    AGroup : Term → Atom
```

**Project Step 3.*** Use one of the formally verified parser implementation approaches do define a parser for the language.
