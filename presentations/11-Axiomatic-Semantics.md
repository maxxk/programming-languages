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

6. [Ynot](http://ynot.cs.harvard.edu) — Imperative programming with Hoare logic in Coq.
7. Chen H. et al. Using Crash Hoare logic for certifying the FSCQ file system. ACM Press, 2015. P. 18–37.
8. Lectures: http://web.eecs.umich.edu/~weimerw/2017-590/lectures/weimer-gradpl-08.pdf
http://web.eecs.umich.edu/~weimerw/2017-590/lectures/weimer-gradpl-09.pdf


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
===============================

Correctness of axiomatic semantics is usually defined in terms of programming language operational semantics. 

More formally, the connection between axiomatic and operational semantics is defined in following way:

- Assertion validity judgement ⊨ — relation between operational semantics configurations and assertions ( σ ⊨ A means "assertion A holds in configuration σ" ) 

- Hoare triple semantics (partial): ⊨ { A } c { B } is defined as:
  For all σ ∈ Σ, for all σ' ∈ Σ, if σ ⊨ A and σ (c)⇒ σ', then σ' ⊨ B

- Hoare triple semantics (total): ⊨ [ A ] c [ B ] is defined as:
  * partial semantics (A ⇒ B)
  * existence of σ'

Assertion language
==================


Derivation rules
================

Imp axiomatic semantics 
=======================

Assignment
==========

Conditional operator
====================

Loop invariants
===============

Soundness
=========

Simultaneous induction
======================

Completeness
============

Weakest preconditions
=====================

Weakest preconditions
=====================

Partial order over assertions
=============================

Fixed points for assertions
===========================

Loop weakest preconditions
==========================

Annotations
===========

Loop invariant soundness
========================

Application of axiomatic semantics
==================================

1. Automated verification tools (especially annotation-based).
2. Code contracts
  - based on the idea of axiomatic semantics, but I'm not aware about any formal proofs of soundness 
  - original implementation in Eiffel programming language (e.g. [Section 8](https://archive.eiffel.com/doc/online/eiffel50/intro/language/tutorial-09.html#pgfId-514761) in Eiffel tutorial)
    `require` statement (precondition), `ensure` statement (postcondition), `invariant` (class state invariant)
  - probably most well-known implementation is [.NET CodeContracts](https://github.com/Microsoft/CodeContracts) (see also [publications](http://research.microsoft.com/en-us/projects/contracts/))
  
CodeContracts examples
======================


```csharp
private static void Main(string[] args)
{
    DoRequiresForAll(new List<string>() {"test",null,"foo"});
}
 
public static void DoRequiresForAll(List<string> input)
{
    Contract.Requires(Contract.ForAll(input, w => w != null));
    input.ForEach(Console.WriteLine);
}
```

```csharp
public static string TrimAfter(string value, string suffix)
{
    Contract.Requires(!String.IsNullOrEmpty(suffix));
    Contract.Requires(value != null);
    Contract.Ensures( !Contract.Result<string>().EndsWith(suffix));
```

Homework assignments
********************

**Task 11.1** (2*) Write axiomatic semantics for a hypothetical functional programming language based on simply-typed lambda calculus with data types and predefined functions over data types.
