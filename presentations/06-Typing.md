# Software and Programming Language Theory
## Typing

<style>
.twocolumn {
  -moz-column-count: 2;
  -webkit-column-count: 2;
}
.small { font-size: small !important; }
.smaller { font-size: 0.8em !important; }
.large { font-size: 1.5em !important; }
.huge { font-size: 2em !important; }
.inference table {
    display: inline-block;
    padding: 1em;
}

.inference table th {
    font-weight: normal;
    border-bottom: 2px solid black;
}
.ib {
    display: inline-block;
}
</style>

https://maxxk.github.io/programming-languages/

maxim.krivchikov@gmail.com

# Literature
- R. Harper, Practical Foundations for Programming Languages, 2nd ed, 2016.
- B. Pierce. Types and Programming Languages. 2002.


# Type system
> A type system is a tractable syntactic method for proving the absence of certain program behaviors by classifying phrases according to the kinds of values they compute.

> B. Pierce. Types and Programming Languages. 2002.

# Typing
> The role of type system is to impose constraints on the formation of phrases that are sensitive to the context in which they occur.

In practice, typing is usually specified by means of the formal inference system:

- typing judgement "in context Γ the expression $x$ has type $τ$" — Γ ⊦ x : τ
- context Γ — set of typing judgement for already defined variables or expressions
- Γ, x : a ⊦ y : b

# Simply Typed Lambda Calculus {.inference}
Type: τ ≡ *b* | τ_1 → τ_2, 

*b* is an element of the set of basic types.

Typing rules:

   $\qquad$
 ----------------
   Γ, x : α ⊦ x : α

   $c_α$ — constant of type α
 -----------------------------
   Γ ⊦ c: α


   Γ, x : σ ⊦ e : τ
 -----------------------------
   Γ ⊦ (λ $x_σ$ . e) : σ → τ 


   Γ ⊦ x : σ → τ  $\qquad$ Γ ⊦ y : σ  
 ------------------------------------
   Γ ⊦ x · y : τ

Derivation — tree of rule applications which starts from empty context and ends in typing derivation for required expression.

# Example from Harper's Practical Foundations... {.inference}
Type: τ ≡ num | str

Expression: e ≡ x *(variable)* | n | <div class="ib">s *(number and string literals)*</div> | <div class="ib">e + f  *(addition)*</div> | <div class="ib">e ^ f *(concatenation)*</div> | <div class="ib">|e| *(length)*</div> | <div class="ib">let x be e in f *(definition)*</div>

Typing:

    $\qquad$
 ----------------
   Γ, x : α ⊦ x : α

    $\qquad$
 ----------------
   Γ ⊦ s : str

    $\qquad$
 ----------------
   Γ  ⊦ n : num

   Γ ⊦ e : num $\qquad$ Γ ⊦ f : num
  -----------------------------------
    Γ ⊦ e + f : num

  Γ ⊦ e : str $\qquad$ Γ ⊦ f : str
 ----------------------------------
    Γ ⊦ e ^ f : str

    Γ ⊦ e : str
   -----------------
    Γ ⊦ |e| : num

  Γ ⊦ e : τ $\qquad$ Γ, x : τ ⊦ f : σ
 -------------------------------------
  Γ ⊦ let x be e in f : σ

# Soundness and completeness
The following properties connect typing with program execution.

**Soundness: ** *No incorrect programs are allowed.*

**Completeness: ** *No correct programs are rejected.*

**Decidability of type checking:** *Type checking is decidable*

We can define a type checking as a decision procedure for property "expression $e$ has type $τ$ but it evaluates to value $v$ of type $φ$ ($φ ≠ τ$)". For this definition:

- soundness means lack of false negatives (if expression is typed, the result of its evaluation has the same type)
- completeness means lack of false positives (if expression $e$ always evaluates to a value of type $τ$, $e : τ$ is derivable during type checking)

Usually type systems for practical languages are sound and possibly decidable, hence incomplete (by Rice theorem of non-trivial property undecidability).

# Soundness
**Soundness: ** *No incorrect programs are allowed.*

Formal statement:  if $Γ ⊢ e : τ$ and during program execution expression $e$ evaluates to a value $v$ then $v$ is of type $τ$.

Soundness is generally proved in two steps: preservation and progress.

$e₁ ⟶ e₂$ — during a single step of evaluation expression $e₁$ evaluates to expression $e₂$ (part of dynamic semantics, more formal definition on next lecture).

**Preservation: ** Evaluation preserves typing.

Formally: $Γ ⊢ e₁ : τ$, $e₁ ⟶ e₂$ ⇒ $Γ ⊢ e₂ : τ$

**Progress: ** If well-typed expression is not a value, it is possible to make an evaluation step.

Formally: $Γ ⊢ e₁ : τ$, $e₁$ is not value ⇒ ∃ $e₂$ : $e₁ ⟶ e₂$.

# Logical properties of typing
(usually are proved either by induction on rules or by induction on derivation)

**Unicity: ** *For every typing context Γ and expression e there exists at most one τ such that $Γ ⊦ e : τ$.*

We usually want this property in a sane type system, it may be neccessary to use a different statement in case of subtyping (not a single type, but a single minimal/maximal type).

**Inversion: ** *(example) If Γ ⊦ plus(a, b) : num then Γ ⊦ a : num, Γ ⊦ b : num. *

If typing rules are complex, such principles are difficult to state and prove. But these principle is essential for e.g. type inference.


# Structural properties of typing {.inference}

**Weakening: ** If Γ ⊦ e : τ then Γ, x : σ ⊦ e : τ for x ∉ Γ.
(we may add to context any number of typing judgements which do not overwrite the types of existing variables)



**Contraction: ** (we may remove repeating judgements from context) 

  Γ, x : A, x : A ⊦ Σ
 ---------------------
   Γ, x : A ⊦ Σ

**Exchange: ** (independent typing judgements may change order in context)

  Γ_1, **x : A,** Γ_2, *y : B,* Γ_3 ⊦ Σ
 ---------------------------------------
  Γ_1, *y : B,* Γ_2, **x : A,** Γ_3 ⊦ Σ


**Substitution: ** (expressions with the same type may be substituted) 

If Γ, x : τ ⊦ e' : τ' and Γ ⊦ e : τ , then Γ ⊦ [e/x]e' : τ'


**Decomposition: ** (we can factor out some typed value)

If Γ ⊦ [e/x]e' : τ' then for every τ such that Γ ⊦ e : τ, we have Γ, x : τ ⊦ e' : τ'

# Substructural type systems
Some type systems lack support for some of the mentioned structural properties.

## Linear types
Based on the linear logic, ensures that each object is used exactly once. I.e. all objects have static lifetime controlled by type system. Doesn't support weakening and contraction.

## Affine types
Linear types with weakening (some variables may stay unused)

# Affine types

In C++ affine types are implemented as `std::unique_ptr`:
```c++
std::unique_ptr<int> p1(new int(5));
std::unique_ptr<int> p2 = p1; //Compile error.
std::unique_ptr<int> p3 = std::move(p1); //Transfers ownership. p3 now owns the memory and p1 is rendered invalid.

p3.reset(); //Deletes the memory.
p1.reset(); //Does nothing.
```

<div class="small">See also: http://homepages.inf.ed.ac.uk/wadler/topics/linear-logic.html</div>

# Linear types
`Clean` programming language
```haskell
AppendAB :: File -> (File, File)
AppendAB file = (fileA, fileB)
where
    fileA = fwritec 'a' file
    fileB = fwritec 'b' file -- compile error
```

```haskell
WriteAB :: File -> File
WriteAB file = fileAB
where
    fileA = fwritec 'a' file
    fileAB = fwritec 'b' fileA
```


```haskell
AppendAorB :: Bool *File -> *File
AppendAorB cond file
    | cond = fwritec 'a' file
    | otherwise = fwritec 'b' file
```


<div class="small">http://www.inf.ufsc.br/~joao.bosco.mota.alves/cleanBookI.pdf</div>

# Lifetimes and borrow checking (Rust)
Linear types provide simple, almost-syntactical value lifetime checking.
More powerful lifetime tracking techniques are required in practice.

Patina: A Formalization of the Rust Programming Language
ftp://ftp.cs.washington.edu/tr/2015/03/UW-CSE-15-03-02.pdf

# Type inference
In some languages it is possible to leave "holes" in place of type specifiers. Type checker tries to fill these holes in process of **type inference (type reconstruction).**

```c++
std::vector<std::map<int, bool>> m;
// ...
for (std::vector<std::map<int,bool>>::const_iterator it = v.begin();
  it != v.end(); ++it) { /* ... */ }

for (auto it=v.begin(); it != v.end(); ++it) {
  decltype(it) it2;
  /* ... */
}
```

# Type inference
```haskell
data List t = Nil | Cons (t, List t)

length l =
  case l of
    Nil -> 0
    Cons(x, xs) -> 1 + length(xs)


-- length :: List t -> Integer
```

# Hindley-Milner Algorithm W
Most well-known algorithm for type inference: Hindley-Milner Algorithm W.

Algorithm is "bottom-up": it assigns type variables starting from leaves of AST.
Monotype: ordinary type, polytype has some type variables: ∀α.(Set α) → int

![](images/83c9167b8fdcefe88fce22e0b1761460.png)

Sample implementations: https://github.com/tomprimozic/type-systems

# Algorithm M
"Algorithm M" is a top-down algorithm for type inference. It stops earlier than W if the input term is ill-typed and in some cases yields better type errors.


Oukseh Lee and Kwangkeun Yi. 1998. Proofs about a folklore let-polymorphic type inference algorithm. ACM Trans. Program. Lang. Syst. 20, 4 (July 1998), 707-723. http://dx.doi.org/10.1145/291891.291892 

# Subtyping {.inference}
Type inference algorithms are undecidable in case of subtyping

Subsumption rule for subtyping:

  Γ ⊦ t : T $\qquad$ T <: U
 ---------------------------
   Γ ⊦ t : U

# Row polymorphism
Row polymorphism is "duck typing" implementation for records (structures, named tuples).

Usual definition for structure type:
[ $t_1$ : $T_1$, $t_2$ : $T_2$, ... ]

```javascript
{ a:1, b: "B"  } : [ a : Integer, b : String ]
```

Row polymorphism:
[ $t_1$ : $T_1$, $t_2$ : $T_2$, ... |  τ]
τ may be instantiated to extra fields.

```javascript
 { a:1, b: "B" } : [ a : Integer | τ ]
 { a:1, b: "B" } : [ b: String | τ ]

function f(x) { return x.a + 1 }
f : [ a : Integer | τ ] → Integer

function g(x) { return x.b + "!" }
g : [ b : String | τ ] → String
```

# Dynamic type checking  
Dynamic type checking is an implementation detail (for strongly-typed programming languages).

For example, GHC compiler for Haskell programming language has a flag 
`-fdefer-type-errors`:

```haskell
{-# OPTIONS_GHC -fdefer-type-errors #-}
f :: Int -> Bool
f 1 = False
f 2 = not 'x' -- boolean "not" operator on string
```

```
/Users/rae/work/blog/011-dynamic/post.lhs:32:13: warning:
    • Couldn't match expected type ‘Bool’ with actual type ‘Char’
    • In the first argument of ‘not’, namely ‘'x'’
      In the expression: not 'x'
      In an equation for ‘ex1’: ex1 = not 'x'
```

If we call `(f 2)`:
```
*** Exception: /Users/rae/work/blog/011-dynamic/post.lhs:32:13: error:
    • Couldn't match expected type ‘Bool’ with actual type ‘Char’
    • In the first argument of ‘not’, namely ‘'x'’
      In the expression: not 'x'
      In an equation for ‘ex1’: ex1 = not 'x'
(deferred type error)
```

<div class="small">https://typesandkinds.wordpress.com/2016/01/22/haskell-as-a-gradually-typed-dynamic-language/</div>

# Gradual typing {.inference}
Gradual typing is a seamless combination of dynamic and static type checking.

Gradual typing introduces a special *unknown* type (usually denoted as **?** or `Any`) with weakened form of type equivalence:

    $\qquad$
  ----------------
     T ~ T
    
     $\qquad$
  ----------------
    T ~ **?**

    $\qquad$
  -----------------
     **?** ~ T

    T ~ U $\qquad$ V ~ W
  -------------------------
    T → V  ~  U → W

Instances of an unknown type coercion to a known type are replaced with dynamic type checking.
Implementations: TypeScript, Flow (for JavaScript); mypy (Python); Typed Clojure, Typed Racket

# Implementation of typing
Static semantics environments → Typing environments

Typing judgements are represented as indexed inductive type constructor. To each typeable node of static semantics tree we must attach the corresponding typing judgement.

Example: http://mazzo.li/posts/Lambda.html

# Recursive types {.inference}

Example: simply-typed lambda calculus with iso-recursive types (B. Pierce. Types and Programming Languages, Chapter 20).

Additional terms:  fold \[T\] t, unfold \[T\] t (T — type)

Additional value: fold \[T\] v (T — type)

Additional types: X (type-variable), μX.T (recursive type)

Additional evaluation rules:

        $\qquad$
  ------------------------------
    unfold [S] (fold [T] v) ⟶ v 

       t ⟶ t'
  -----------------------------
     fold [T] t ⟶ fold [T] t'


     t ⟶ t'
  --------------------------------
    unfold [T] t ⟶ unfold [T] t'

Additional typing rules:

    U = μX.T $\qquad$  Γ ⊦ t : [X := U] T 
  ----------------------------------------
          Γ ⊦ fold [U] t : U
  
   U = μX.T $\qquad$  Γ ⊦ t : U 
  ----------------------------------------
          Γ ⊦ unfold [U] t : U [X := U] T 

# Recursive types: example

Type of lists with argument — natural number.

Primitive types: ℕ, 𝟙 (type with single element, 1).

Additional type constructors: A + B (alternative), A * B (pairs)

ℕ-List : Type := μX.(1 + ℕ * X)

nil : ℕ-List := fold [ℕ-List] (inl 1)

cons : ℕ → ℕ-List → ℕ-List := λ (n : ℕ). λ (l : ℕ-List). fold [ℕ-List] (inr (n, l))

head : ℕ-List → 1 + ℕ := λ (l : ℕ-List). case (unfold [ℕ-List] l) of

$\qquad$ inl _ ⇒ inl 1

$\qquad$ inr (h, t) ⇒ inr h


# System F: polymorphic lambda-calculus {.inference}

Additional types: α (type variable), ∀α.T (polymorphic type)

Additional terms: Λα.t (type abstraction), t ∘ A (type application)

Additional typing rules:


    Γ ⊦ t : B 
  -------------------
    Γ ⊦ Λα.t : ∀α.B
  
   Γ ⊦ t : ∀α.B
  -------------------------
    Γ ⊦ t ∘ A : B[α := A]

Example: polymorphic identity function

Λα. λ(x : α). x : ∀α. α → α

# Data-type encoding in polymorphic lambda-calculus

In polymorphic lambda-calculus it is possible to define some data types 
in terms of elimination functions.


## Boolean numbers

Bool = ∀ γ. (γ → γ → γ)

true = Λ γ. λ t. λ f. t

false = Λ γ. λ t. λ f. f

if (u : Bool) then (T : A) else (F : A) = (u ∘ A) T F

## Sum-types (disjoint unions)

A + B = ∀ γ. ( (A → γ) → (B → γ) → γ)

inl a = Λ γ. λ f. λ g. (f a)

inr b = Λ γ. λ f. λ g. (g b)


# Homework assignments

**Task 6.1.\*\*** Implement Algorithm W for Hindley-Milner type inference in polymorphic lambda-calculus with data types.

**Task 6.2.\*\*** Implement Algorithm M for type inference in polymorphic lambda-calculus with data types.

**Task 6.3.\*\*\*** Implement a type inference algorithm for a language with row polymorphism.

**Task 6.4.\*\*\*** Implement a type checking algorithm for a language with gradual typing.

<!--
# Project

**Project Step 4.** Design a type system for your programming language.

*easy* — on paper.

*medium* — on paper with type checker implementation (see also [previous semester](https://maxxk.github.io/formal-models/presentations/06-Lambda-cube.html#двусторонний-алгоритм-проверки-типов)).

*hard* — as an inductive type in Agda or Coq.

*nightmare :)* — PHOAS in Agda or Coq, like in in example from previous slide: http://mazzo.li/posts/Lambda.html
-->
