Software and Programming Language Theory
****************************************

Axiomatic Semantics
===================

https://maxxk.github.io/programming-languages/

maxim.krivchikov@gmail.com



Axiomatic semantics literature
******************************

1. *Chapter 6 of* **Winskel G. The Formal Semantics of Programming Languages. Cambridge, Massachusetts, US: MIT Press, 1993. xx+361 p.**
2. *Part IV of* Шилов Н.В. Основы синтаксиса, семантики, трансляции и верификации программ: учебное пособие. Новосибирск: НГУ, 2011. 292 p.

Bibliography
============

3. Floyd R.W. Assigning Meanings to Programs // Mathematical Aspects of Computer Science / ed. Schwartz J.T. American Mathematical Society, 1967. Vol. 19. P. 19–32.
4. Hoare C.A.R. An axiomatic basis for computer programming // Communications of the ACM. 1969. Vol. 12, № 10. P. 576–580.
5. Dijkstra E.W. Guarded commands, nondeterminacy and formal derivation of programs // Communications of the ACM. 1975. Vol. 18, № 8. P. 453–457.


Further reading
===============

6. Ynot (http://ynot.cs.harvard.edu) — Imperative programming with Hoare logic in Coq.
7. Chen H. et al. Using Crash Hoare logic for certifying the FSCQ file system. ACM Press, 2015. P. 18–37.
8. `Lecture 1 <http://web.eecs.umich.edu/~weimerw/2017-590/lectures/weimer-gradpl-08.pdf>`_,
   `Lecture 2 <http://web.eecs.umich.edu/~weimerw/2017-590/lectures/weimer-gradpl-09.pdf>`_.


Axiomatic semantics
*******************

Axiomatic semantics describes semantics of programming language in terms of predicate transformation.

In contrast with operational and denotational semantics, axiomatic semantics is concerned with proving correctness of specific programs.

Operational and denotational semantics are mostly concerned with properties of programming language as a whole.

Axiomatic semantics consists of:

- language of assertions about program state (usually first-order logic, possibly other formal inference system)
- rules of derivation 

Hoare triples
*************

{A} c {B}
=========

- A — precondition (assertion)
- c — statement of the programming language
- B — postcondition (assertion)

Informal semantics: if A holds immediately before c is executed, then B must hold immediately after c is executed.


Axiomatic semantics correctness
*******************************

Correctness of axiomatic semantics is usually defined in terms of programming language operational semantics. 

The connection between axiomatic and operational semantics is defined in following way:

- Assertion validity judgement ⊨ — relation between operational semantics configurations and assertions ( σ ⊨ A means "assertion A holds in configuration σ" ) 

- Hoare triple semantics (partial): ⊨ { A } c { B } is defined as:
  For all σ ∈ Σ, for all σ' ∈ Σ, if σ ⊨ A and σ (c)⇒ σ', then σ' ⊨ B

- Hoare triple semantics (total): ⊨ [ A ] c [ B ] is defined as:
  * partial semantics ⊨ { A } c { B }
  * existence of σ'


Example: Imp'
*************

Simple imperative language with arithmetical and boolean expressions, assignment, conditional operator and loop.

Abstract syntax (omitting the operator precedence and lexical structure):

.. code::

    Program = Statement "return" VariableName

    Statement = VariableName "=" ArithmeticalExpression
    | "skip"
    | Statement (";" | "\n") Statement
    | "if" "(" BooleanExpression ")" "{ " Statement " } else { " Statement " }"
    | "while "(" BooleanExpression ")" "{ " Statement  "}"

    ArithmeticalExpression = VariableName
    | IntegerNumber
    | "sqrt" ArithmeticalExpression
    | "-" ArithmeticalExpression
    | ArithmeticalExpression ("+" | "-" | "*" | "/" | "%") ArithmeticalExpression

    BooleanExpression = ArithmeticalExpression ("<" | ">" | "==") ArithmeticalExpression
    | "!" BooleanExpression
    | BooleanExpression ("&&" | "||" | "^^")


Assertion language
******************

First order logic over Imp' boolean expressions:

A = true | false | BooleanExpression | A₁ ∧ A₂ | A₁ ∨ A₂ | A₁ ⇒ A₂ | ∀x.A | ∃x.A  | ¬A | e₁ = e₂

Quantification is over variables (x).


Assertion validity
******************

Definition of relation σ ⊨ A:

- σ ⊨ true 
- σ ⊨ A₁ and σ ⊨ A₂ ⇔ σ ⊨ A₁ ∧ A₂
- σ ⊨ A₁ ⇒ σ ⊨ A₁ ∨ A₂
- σ ⊨ A₂ ⇒ σ ⊨ A₁ ∨ A₂
- σ ⊨ A₁ implies σ ⊨ A₂ ⇔ σ ⊨ A₁ ⇒ A₂
- ∀ n ∈ ℤ σ[x ≔ n] ⊨ A ⇔ σ ⊨ ∀x.A 
- ∃ n ∈ ℤ σ[x ≔ n] ⊨ A ⇔ σ ⊨ ∃ x.A

Assertion derivation
********************

To check σ ⊨ A we need to evaluate program in terms of operational semantics (dynamic verification). 

To use axiomatic semantics for static verification we need to define formal inference system for Hoare triple derivation.

Derivation judgement ⊢ A on assertions is defined as usual for first-order logic.

Axiomatic semantics is defined as derivation judgement of Hoare triples. 

We assume the following rule of consequence ("transitivity" of derivations):

**If** ⊢ A' ⇒ A, ⊢ B ⇒ B', ⊢ {A} c {B} **then** ⊢ {A'} c {B'}

Imp' axiomatic semantics
************************

1. Skip statement: ⊢ {A} ``skip`` {A}
2. Assignment: ⊢ {A[x:=e]} ``x = e`` {A}
3. Sequence: **If** ⊢ {A} ``c₁`` {B}, ⊢ {B} ``c₂`` {C} **then** ⊢ {A} ``c₁ ; c₂`` {C} 
4. Conditional operator: **If** ⊢ { A ∧ ``b`` } ``c₁`` {B}, ⊢ { A ∧ ¬``b`` } ``c₂`` {B} **then** ⊢ {A} ``if (b) { c₁ } else { c₂ }`` {B}
5. Loop: **If** ⊢ { A ∧ ``b`` } ``c`` {A} **then** ⊢ {A} ``while (b) { c }`` {A ∧ ¬``b``}

Derivable rules:

- "forward" axiom for assignment: ⊢ {A} ``x = e`` { ∃ x₀. A[ ``x`` := x₀]  ∧ ``x`` = ``e``[``x`` := x₀] } 
- loop invariant for loops: **If** ⊢ A ∧ b ⊢ C, ⊢ {C} ``c`` {A}, ⊢ A ∧ ¬ ``b`` ⇒ B **then** ⊢ {A} ``while (b) { c }`` { B }

Assignment
**********
- aliasing: situation in which single data location in memory can be accessed through different names (aliases) in program.

Example (C): ``int i, *j = &i, *k = &i;``

In axiomatic semantics the following holds: { true } ``*j = 5`` {``*i + *j`` = 10}


Soundness
*********

Soundness for axiomatic semantics (derivable properties are observable):

⊢ {A} c {B} ⇒ ⊨ {A} c {B}
=========================

This statement contains three inductively-defined objects:

1. c — program statement
2. ⊨ {A} c {B} — operational semantics derivation (sequence of rule applications)
3. ⊢ {A} c {B} — axiomatic derivation

Obvious proofs by induction on the structure of each of these objects won't work.

Corner cases are ``while`` loops and rule of consequence.

Simultaneous induction
**********************

⊢ {A} c {B} ⇒ ⊨ {A} c {B}

Let "<" denote the substructure relation on inductive types (x < y ⇒ x is substructure of y). Elements of an inductive type with substructure relation form partial order.

Let "⊂" denote the lexicographic ordering on tuples of (different) inductive type elements:

(o, a) ⊂ (o', a') ≡ o < o' or (o = o' and a < a')

"⊂" is a well-founded order and we may use it to prove statements by induction with hypothesis "valid for all tuples t ⊂ X" (X is induction step variable).

We can prove soundness for axiomatic semantics by simultaneous induction on the tuple (operational semantics derivation, axiomatic derivation).

Completeness
************

Axiomatic semantics is complete relative to operational semantics if:

⊨ {A} c {B} ⇒ ⊢ {A} c {B}
=========================

Weakest preconditions
*********************

(Dijkstra)

To verify that {A} c {B}:

1. Find all pre-conditions A':  ⊨ {A'} c {B}  (Pre(c, B))
2. For one A' ∈ Pre(c, B) prove that ⊢ A ⇒ A'

We can define partial order over assertions by means of implication:

A ⊑ A' ⇔ A' ⇒ A

If Pre(c, B) has least upper bound under this order, we call it weakest precondition:

WP(c, B) = lub Pre(c, B)

⊢ A ⇒ WP(c, B)

Special case: ``while`` loop, we need to use fixed point theorem (denotational semantics)


Relative Completeness: Expressiveness
*************************************

We can define weakest precondition in terms of configurations: wp(c, B) = { σ | σ (c)⇒σ' | σ' ⊨ B }.

Then we can say that assertion language is **expressive** if for any command and any postcondition there is a precondition which is valid exactly on weakest precondition in terms of configurations. 


Verification Conditions
***********************

Weakest common preconditions are hard to compute (e.g. ``while`` loop). 

To make tools for automatic property checking, we can use user input (e.g. loop invariants) to compute "weak enough" preconditions: verification conditions.


Application of axiomatic semantics
**********************************

1. Automated verification tools (especially annotation-based).
2. Code contracts
  - based on the idea of axiomatic semantics, but I'm not aware about any formal proofs of soundness 
  - original implementation in Eiffel programming language (e.g. [Section 8](https://archive.eiffel.com/doc/online/eiffel50/intro/language/tutorial-09.html#pgfId-514761) in Eiffel tutorial)
    `require` statement (precondition), `ensure` statement (postcondition), `invariant` (class state invariant)
  - probably most well-known implementation is [.NET CodeContracts](https://github.com/Microsoft/CodeContracts) (see also [publications](http://research.microsoft.com/en-us/projects/contracts/))
  
CodeContracts examples
**********************


.. code:: csharp

    private static void Main(string[] args)
    {
        DoRequiresForAll(new List<string>() {"test",null,"foo"});
    }
    
    public static void DoRequiresForAll(List<string> input)
    {
        Contract.Requires(Contract.ForAll(input, w => w != null));
        input.ForEach(Console.WriteLine);
    }


.. code:: csharp

    public static string TrimAfter(string value, string suffix)
    {
        Contract.Requires(!String.IsNullOrEmpty(suffix));
        Contract.Requires(value != null);
        Contract.Ensures( !Contract.Result<string>().EndsWith(suffix));


Homework assignments
********************

**Task 11.1** (2*) Write axiomatic semantics for a hypothetical functional programming language based on simply-typed lambda calculus with data types, conditional operator and predefined functions over data types.

**Task 11.2** (2*) Write axiomatic semantics for assignment operator in Imp' extended with aliasing operation.
