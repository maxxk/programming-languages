# Software and Programming Language Theory
## Axiomatic Semantics
## Concluding remarks

<style>
.twocolumn {
  -moz-column-count: 2;
  -webkit-column-count: 2;
}
.small { font-size: small !important; }
.smaller { font-size: 0.8em !important; }
.large { font-size: 1.5em !important; }
.huge { font-size: 2em !important; }
.reveal section {
  text-align: left;
}
.reveal section.center {
  text-align: center;
}

.xits {
  font-family: "XITS Math", "XITS", "STIX", "PT Serif Caption", sans-serif !important;
}
</style>

Course page: https://maxxk.github.io/programming-languages/
Contact author: maxim.krivchikov@gmail.com



# Axiomatic semantics literature
1. *Chapter 6 of* **Winskel G. The Formal Semantics of Programming Languages. Cambridge, Massachusetts, US: MIT Press, 1993. xx+361 p.**
2. *Part IV of* Шилов Н.В. Основы синтаксиса, семантики, трансляции и верификации программ: учебное пособие. Новосибирск: НГУ, 2011. 292 p.

Bibliography:
3. Floyd R.W. Assigning Meanings to Programs // Mathematical Aspects of Computer Science / ed. Schwartz J.T. American Mathematical Society, 1967. Vol. 19. P. 19–32.
4. Hoare C.A.R. An axiomatic basis for computer programming // Communications of the ACM. 1969. Vol. 12, № 10. P. 576–580.
5. Dijkstra E.W. Guarded commands, nondeterminacy and formal derivation of programs // Communications of the ACM. 1975. Vol. 18, № 8. P. 453–457.


Further reading:
6. [Ynot](http://ynot.cs.harvard.edu) — Imperative programming with Hoare logic in Coq.
7. Chen H. et al. Using Crash Hoare logic for certifying the FSCQ file system. ACM Press, 2015. P. 18–37.

# Axiomatic semantics
Today we will use the following presentation (based on Winskel book) https://classes.soe.ucsc.edu/cmps203/Winter11/08-axiomatic.ppt.pdf

# Application of axiomatic semantics
1. Automated verification tools (especially annotation-based).
2. Code contracts
  - based on the idea of axiomatic semantics, but I'm not aware about any formal proofs of soundness 
  - original implementation in Eiffel programming language (e.g. [Section 8](https://archive.eiffel.com/doc/online/eiffel50/intro/language/tutorial-09.html#pgfId-514761) in Eiffel tutorial)
    `require` statement (precondition), `ensure` statement (postcondition), `invariant` (class state invariant)
  - probably most well-known implementation is [.NET CodeContracts](https://github.com/Microsoft/CodeContracts) (see also [publications](http://research.microsoft.com/en-us/projects/contracts/))
  
# CodeContracts examples


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