# Software and Programming Language Theory
## Programming Language Specification
## Formal syntax

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

# Additions
## Optional typing systems
In similar way to the annotations of model checking tools, the optional type checkers do not neccessarily accept all correct programs.

```javascript
/*::
type BinaryTree =
  { kind: "leaf", value: number } |
  { kind: "branch", left: BinaryTree, right: BinaryTree }
*/

function sumLeaves(tree /*: BinaryTree*/): number {
  if (!tree.left) { // condition was: kind == "leaf"
    return tree.value;
  } else {
    return sumLeaves(tree.left) + sumLeaves(tree.right);
  }
}
```

# Addition
## Remember: Computer Science is old
- First programming languages and compilers apperared in 1950s.
- Classical textbooks, e.g. the first edition of "Dragon book" (Aho, Ullman. Principles of Compiler Design) appeared in 1977.
- Commonly-known technology and mathematics may be outdated
- Example: standard character set: machine-specific character set → ANSI (C) → UCS-2 (C#, JavaScript) → UTF-8
```
"👍".length = 2
```

# Programming language specification
Linguistics in general is concerned with three main aspects of a written language:
1. **Syntax** — the way in which linguistic elements (words) are put together to form constituents (phrases).
2. **Semantics** — the meanings of words and phrases in language.
3. **Pragmatics** — the relationship between sentences to the environment in which they occur.

(non-computer languages also have:
- *morphology* — the study of language morphemes, words; the study and description of word formation (as inflection, derivation, and compounding) in language
- *phonology* — the study of the speech sounds used in a language

  )
<span class="smaller">Definitions from [Merriam-Webster](http://www.merriam-webster.com/dictionary/)
See also: http://www.sbql.pl/Topics/Syntax%20semantics%20and%20pragmatics.html
</span>


# Syntax
Syntax of a programming language is concerned with the form of programs: how expressions, commands, declarations and other constructs must be arranged to make a well-formed program.

<span class="small">Definition from D. Watt — Programming Language Design Concepts</span>

How can we write the program and how the compiler/interpreter expects it to be formed.

# Syntax: example {#syntax-example}
<span class="small">Example based on F. Turbak, D. Gifford. Design Concepts in Programming Languages</span>
Suppose we want to compute the sum of the product of *v* and *w* and the quotient of *y* and *z*.
Syntax representations:
> - mathematical expression: $v·w + \dfrac{y}{z}$
> - traditional infix notation: `v*w + y/z`
> - LISP prefix S-expression: `(+ (* v w) (/ y z))`
> - reverse polish notation / stack calculator: `v w * y z / +`
> - some [visual "syntax"](https://scratch.mit.edu/projects/102624309/#editor)
> ![](images/scratch-syntax.png)

# Semantics
Semantics of a programming language is concerned with the meaning of programs: how a well-formed program may be expected to behave when executed on a computer.

<span class="small">Definition from D. Watt — Programming Language Design Concepts</span>

We will spend a half of the course on this topic, but today we speak about syntax.

# Semantics: example
A well-formed formula `x*y + z` may have meaning:

> - compute the specified formula to get a numerical value as an answer for specific values of *x, y, z*
>     - integral operations for integers
>     - floating-point operations for floating-point number
>     - logical or lattice operations (disjunction and conjunction, max and min)
> - (python) replicate *y* times the string stored in *x* and concatenate it with *z* converted to string
> - just a symbolic expression for the further optimization
> - etc.

# Pragmatics
A programming language's pragmatics is concerned with the way in which the language is intended to be used in practice.
<span class="small">Definition from D. Watt — Programming Language Design Concepts</span>

No general consensus on the definition.
Two sides:
> - human interface (how the programmers are expected to design and implement programs in practice: design patterns, concurrency support and specific model, foreign function interfaces, packaging system; exception-based control flow :)
> - implementation details (specific garbage collection algorithm, semantics-preserving optimizations)

. . .

Evaluation strategy (strict / lazy) is a part of semantics (because the change of strategy changes the meaning of the same syntactic formula).

# Formal specification
Our objective is to get a precise mathematical description of a program.

Each part of language specification must be implemented in mathematical terms.

# Syntax
<span class="large">List<Char> ⟶ AbstractSyntaxTree</span>

# Syntax: relation to formal languages
(regular ⊂ context-free ⊂ context-sensitive ⊂ unbounded)

We could make a relation between programs and formal languages in many ways:
- language $L_1$, containing all well-formed (syntactically correct) programs
- language $L_2 ⊆ L_1$, containing all well-typed (runnable) programs
- language $L_3 ⊆ L_2$, containing all semantically-correct programs (running without errors)

It is obvious that for almost every practical programming language $L_3$ is unbounded. For the modern languages, $L_1$ is usually context-free or some subset of context-free languages (Deterministic Context-Free Languages, unambiguous languages which can be accepted by deterministic pushdown automaton and therefore can be parsed in $O(n)$).

$L_2$ may be decidable, but not usually decidability is not proven. Compiler freezes may be caused by undecidability of type checking.

# Lexical analysis
<span class="large">List<Char> ⟶ List<Token></span>

Traditionally decision problem of $L_1$ membership (well-formedness of a program) is split by two parts:
- lexical analysis transforms a sequence of characters to a sequence of tokens (marked groups of characters, like identifiers, integers, etc.):
    <span class="huge">`int main(int argc, char argv)`</span>
    <span class="smaller">`KEYWORD IDENTIFIER LPAR KEYWORD IDENTIFIER COMMA KEYWORD IDENTIFIER RPAR`</span>
- usually lexical specification of the language is regular
- Unicode standard defines base categories for a lexical analysis (identifier start, identifier, digit, punctuation etc.)

# Syntax analysis
<span class="large">List<Token> ⟶ AbstractSyntaxTree</span>

- **concrete syntax**: the syntax of a language including all the features visible in the source code such as parentheses and delimiters;
- **abstract syntax**: the high-level description of a language structure independent of machine-oriented structures and encodings.

![](images/ast-example-js.png)
<span class="small">Source: http://azu.github.io/slide/JSojisan/resources/ast-is-true.png</span>

# Abstract Syntax Tree
— a representation of an abstract syntax of a program
![](images/ast-example.png)

Multiple representations of [concrete syntax](#syntax-example) may correspond to a single abstract syntax.

. . .

Lisp S-expression is a direct representation of abstract syntax tree:
```clojure
(+
  (* v w)
  (/ y z) )
```

# Language specification
Languages (which are (possibly infinite) subsets of strings of some alphabet: Σ*) may have a finite description, which is called "grammar".

- **generative grammar:** how to generate all strings of the language starting from a single "start" symbol
- **analytic grammar:** how to get an abstract syntax tree (a tree of rule applications) from a given string

# BNF: Backus Normal Form
(Backus-Naur Form, by Dijkstra, because "it is not a normal form")
During the development of ALGOL 60, John Backus proposed a notation for context-free grammars:

> - group of characters named *expr* is denoted as `<expr>`;
> - character constants are quoted `"("`
> - sequence of constants and groups defines a sequence `"(" <expr> ")"`
> - group of characters is defined by an operator `::=`:
>   ```
>   <expr> ::= "(" <atom> ")"
>   ```
> - definitions may use recursion:
>   ```
>   <parens> ::= "" | "(" <parens> ")"
>   ```
> - at the right side of the definition operator the alternative operator **|** is allowed:
>   ```
>   <expr> ::= <expr> | <expr> <add-operation> <atom>
>   ```

# BNF example
```bnf
<syntax>         ::= <rule> | <rule> <syntax>
<rule>           ::= <opt-whitespace> "<" <rule-name> ">" <opt-whitespace> "::=" <opt-whitespace> <expression> <line-end>
<opt-whitespace> ::= " " <opt-whitespace> | ""
<expression>     ::= <list> | <list> <opt-whitespace> "|" <opt-whitespace> <expression>
<line-end>       ::= <opt-whitespace> <EOL> | <line-end> <line-end>
<list>           ::= <term> | <term> <opt-whitespace> <list>
<term>           ::= <literal> | "<" <rule-name> ">"
<literal>        ::= '"' <text> '"' | "'" <text> "'"
```

# BNF standars
- Extended BNF (EBNF, specification ISO/IEC 14977)
- Augmented BNF (ABNF, specification IETF [RFC 5324](https://tools.ietf.org/html/rfc5234), [RFC 7405](https://tools.ietf.org/html/rfc7405))

# PEG: Parsing Expression Grammar
Analytical grammar formalism, a superset of regular expressions:
- atomic expressions: empty string ε, terminal symbol (constant), non-terminal symbol (rule reference)
- sequence: $e_1 \; e_2$
- *ordered* choice: $e_1 \; / \; e_2$
- Kleene star (zero or more): $e*$
- "plus" (one or more): $e+$
- optional: $e?$
- positive lookahead: $&e$
- negative lookahead: $!e$



# Left recursion
Most direct PEG implementations do not support left recursion in grammar. Parsing of the following grammar will not terminate:
```
Value   ← [0-9.]+ / '(' Expr ')'
Product ← Expr (('*' / '/') Expr)*
Sum     ← Expr (('+' / '-') Expr)*
Expr    ← Product / Sum / Value
```
It is possible to rewrite grammars without left recursion.

# Infix operators
Infix mathematical notation is probably the only desirable part of syntax which don't have nice representations as an S-expression
```scheme
(defun triangle (number-of-rows)    ; Version with
                                    ;   incrementing counter.
  "Add up the number of pebbles in a triangle.
The first row has one pebble, the second row two pebbles,
the third row three pebbles, and so on.
The argument is NUMBER-OF-ROWS."
  (let ((total 0)
        (row-number 1))
    (while (<= row-number number-of-rows)
      (← total (+ total row-number))
      (← row-number (1+ row-number)))
    total))
```

# Precedence and associativity
Infix operators are complicated for parsing:
- operator precedence: `a * b + c`, `a + b * c`
- associative operators: `a + b + c`
- left-associative operators: division `a / b / c` ⟶ `(a / b) / c`
- right-associative operators: assignment `a = b = c` ⟶ `a = (b = c)`
- non-associative operators: `a < b < c` is invalid in C#

The non-associatitvity is hard to represent in syntax, so it is usually a property of semantics.

# TDOP algorithm

Operator binding power:
`1 + 2 * 4`
- right binding power of "+" is 10
- left binding power of "*" is 20

<div class="smaller">
See also:
- Interactive example http://l-lang.org/blog/TDOP---Pratt-parser-in-pictures/
- simple implementation http://javascript.crockford.com/tdop/tdop.html
- JetBrains Nitra (russian project): https://github.com/JetBrains/Nitra
- original Pratt paper: https://tdop.github.io
</div>

# Attribute grammar
If a rule of the grammar is matched, compute an additional information to attach to an AST node.
```
Expr1 → Expr2 + Term [ Expr1.value = Expr2.value + Term.value ]
Expr → Term [ Expr.value = Term.value ]
Term1 → Term2 * Factor [ Term1.value = Term2.value * Factor.value ]
Term → Factor [ Term.value = Factor.value ]
Factor → "(" Expr ")" [ Factor.value =  Expr.value ]
Factor → integer [ Factor.value = strToInt(integer.str) ]
```
<div class="smaller">
Live example of calculator: http://pegjs.org/online
</div>

# Parser combinators
Special technique of parser implementation for functional languages (required support of higher-order functions)

```
type Parser<U, V> = Iterable<U> → V

or<U,V> : (left : Parser<U, V>, right : Parser<U, V>) → Parser<U, V>
and<U,V> : (left : Parser<U, V>, right : Parser<U, V>) → Parser<U, V>
repeat<U, V> : (p : Parser<U, V>) → Parser<U, Array<V>>
str : string → Parser<string, string>
```

# Parser combinators
Advantage: simple, embedded grammar specification
Disadvantage: hard to reason about
<div class="smaller">
Example implementations:
- [Parsec](http://jakewheat.github.io/intro_to_parsing/) (Haskell)
- [Parsimmon](https://github.com/jneen/parsimmon) (JavaScript)
- [Boost::Spirit](http://www.boost.org/doc/libs/1_60_0/libs/spirit/doc/html/index.html) (C++)
</div>

# Modern algorithms and implementations
Contex-free grammar parsers:

- theoretical complexity bound: reduced by Valiant to boolean matrix multiplication, $O(n^{2.3728639})$ with Coppersmith-Wingorad multiplication;
  <span class="small">Leslie G. Valiant (1975). "General context-free recognition in less than cubic time". Journal of Computer and System Sciences 10 (2): 308–315. doi:10.1016/s0022-0000(75)80046-8.</span>
- LL(k), LR(k), LALR(k) (c.f. Aho, Ullman "Principles of Complier Design" and [ANTLR](http://www.antlr.org) )
- Earley parser (used in NLP): $O(n^3)$ for general context-free grammar, $O(n^2)$ for unambiguous grammar, linear time for some deterministic context-free language
  <span class="small">Aycock, John; Horspool, R. Nigel (2002). Practical Earley Parsing. The Computer Journal 45. pp. 620–630. doi:10.1093/comjnl/45.6.620</span>
- GLR: $O(n^3)$ upper bound, complexity is lower for "more deterministic" grammars, $O(n)$ for deterministic grammars
- [GLL](http://www.cs.rhul.ac.uk/research/languages/csle/GLLsketch.html): recent memoization-based algorithm allowing the parser combinator implementation.

Non-context-free:
- [Refal language](http://www.refal.net/rf5_frm.htm)


# Non-context-free syntax
- C++ (templates a Turing-complete and type-aware)

```c++
template<bool V> struct answer { answer(int) {} bool operator()(){return V;}};

template<bool no, bool yes, int f, int p> struct IsPrimeHelper
  : IsPrimeHelper<p % f == 0, f * f >= p, f + 2, p> {};
template<bool yes, int f, int p> struct IsPrimeHelper<true, yes, f, p> { using type = answer<false>; };
template<int f, int p> struct IsPrimeHelper<false, true, f, p> { using type = answer<true>; };

template<int I> using IsPrime = typename IsPrimeHelper<!(I&1), false, 3, I>::type;
template<int I>
struct X { static const int i = I; int a[i]; };

template<typename A> struct foo;
template<>struct foo<answer<true>>{
  template<int I> using typen = X<I>;
};
template<> struct foo<answer<false>>{
  static const int typen = 0;
};

int main() {
  auto b = foo<IsPrime<234799>>::typen<1>(); // Syntax error if not prime
  return 0;
}
```

<span class="small">http://stackoverflow.com/questions/14589346/is-c-context-free-or-context-sensitive</span>

# Homework assignments
**Task 3.1**** Implement parser combinators a programming language of your choice. Must be implemented at least the following combinators:
- string or regex parser
- sequence (a, b )
- Kleene star
- composition (a ∘ b: parse a, pass result to b; if b fails, whole composition fails)
- lookahead (a | b : parse a, try parse b, if b succeeds, continue parsing from the last part of a)
