---
layout: default
---
Special course for 3rd-6th year students

# Software and Programming Language Theory

### V. A. Vasenin, M. A. Krivchikov

Course topics include:

* history and classification of programming languages
* formal semantics of programming languages
* specification of domain-specific programming languages
* static typing and type inference

**Wednesday, 12:30-14:05, aud. 13-15, starting March 2nd.**

**Attention! Lecture at March 9th is cancelled, next lecture is scheduled for March 16th.**

The course is required for 5th year Computational Mathematics
department students as a course in foreign language.

**2.03.2016** Introduction. History of programming languages. ([presentation - html](presentations/01-Introduction.html))

**16.03.2016** Classification of programming languages. ([presentation - html](presentations/02-Classification.html))

**23.03.2016** Specification of programming language. Syntax. ([presentation - html](presentations/03-Specification-Syntax.html))

**30.03.2016** Formal syntax analysis. Syntax extensions. ([presentation - html](presentations/04-Macros-Parsing.html))

***Update: Task 4.1 *** (6*). Implement Danielsson's Total Parser Combinators in Coq.

**06.04.2016** Static semantics. Naming, Bindings and Scope. ([presentation - html](presentations/05-Static-Semantics.html))

***Update: Tasks and Project Step 4***.
**Task 5.1.** ** Implement a translator from simply-typed lambda calculus <abbr title="Abstract Syntax Tree">AST</abbr> to <abbr title="Higher-Order Abstract Syntax">HOAS</abbr> in Haskell or any other language of your choice (except the original OCaml) which is expressive enough to represent HOAS.

**Task 5.2.** *** Implement a translator from simply-typed lambda calculus <abbr title="Abstract Syntax Tree">AST</abbr> to <abbr title="Parametrized Higher-Order Abstract Syntax">PHOAS</abbr> in Haskell or any other language of your choice (except the original OCaml) which is expressive enough to represent PHOAS.

**Project Step 4.** Implement a static formal semantics for your programming language (in Agda or Coq). Choose one of the following approaches.
- **Project Step 4a.** *  Assign the unique identifiers (e.g. natural numbers) to each bindable identifier leaf in the AST and create the mapping between the unique identifiers and static semantics subtrees.
- **Project Step 4b.** ** Use Higher-Order Abstract Syntax.

**13.04.2016** Typing as a part of static semantics. ([presentation - html](presentations/06-Typing.html))

**20.04.2016** Operational semantics ([presentation - html](presentations/07-Operational-Semantics.html))

**27.04.2016** Operational semantics implementation ([presentation - html](presentations/08-Operational-Semantics-Implementation.html))

**04.05.2016** Denotational semantics ([presentation - html](presentations/09-Denotational-semantics.html))

**11.05.2016** Denotational semantics implementation: Monads ([presentation - html](presentations/10-Monads.html))
