# Software and Programming Language Theory
## Domain-specific languages
## Intermediate representation languages

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

Course page: https://maxxk.github.io/programming-languages/
Contact author: maxim.krivchikov@gmail.com

# Domain-specific languages
https://cacm.acm.org/magazines/2011/7/109910-dsl-for-the-uninitiated/fulltext

Domain-specific programming language is a language which is targetet to the specific application domain.

# Example: HTML and CSS
Domain: text markup (HTML) and hierarchical styling (CSS).
```css
form > label > .redtext {
    color: red;
}

.redtext:hover {
    border: solid green 1px;
}
```

```html
<span class="redtext">No red text</label>
<form>
<label for="name" class="redtext"><span class="redtext">Name</span></label><input type="text" name="name">
<label for="birthday">Birthday</label><input type="date" name="birthday">
</form>
```
<style type="text/css">
form > label > .redtext {
    color: red;
}

.redtext:hover {
    border: solid green 1px;
}

</style>
<span class="redtext">No red text</span>
<form>
<label for="name" class="redtext"><span class="redtext">Name</span></label><input type="text" name="name">
<label for="birthday">Birthday</label><input type="date" name="birthday">
</form>


# Example: UNIX shell
Domain: OS process, input/output management, pipelining

```bash
diff <(ls $first_directory) <(ls $second_directory)


sort -k 9 <(ls -l /bin) <(ls -l /usr/bin) <(ls -l /usr/X11R6/bin)
# Lists all the files in the 3 main 'bin' directories, and sorts by filename.
# Note that three (count 'em) distinct commands are fed to 'sort'.

ping $(hostname)
```



# Example: GrGen {.twocolumn}
Domain: graph modeling and rewriting
http://www.info.uni-karlsruhe.de/software/grgen/
https://en.wikipedia.org/wiki/GrGen

```
node class GridNode {
    food:int;
    pheromones:int;
}
node class GridCornerNode extends GridNode;
node class AntHill extends GridNode {
    foodCountdown:int = 10;
}
node class Ant {
    hasFood:boolean;
}

edge class GridEdge connect GridNode[1] -> GridNode[1];
edge class PathToHill extends GridEdge;
edge class AntPosition;
```

```
rule TakeFood(curAnt:Ant)
{
    curAnt -:AntPosition-> n:GridNode\AntHill;
    if { !curAnt.hasFood && n.food > 0; }
    modify {
        eval {
            curAnt.hasFood = true;
            n.food = n.food - 1;
        }
    }
}

rule SearchAlongPheromones(curAnt:Ant)
{
    curAnt -oldPos:AntPosition-> old:GridNode <-:PathToHill- new:GridNode;
    if { new.pheromones > 9; }
    modify {
        delete(oldPos);
        curAnt -:AntPosition-> new;
    }
}

test ReachedEndOfWorld(curAnt:Ant) : (GridNode)
{
    curAnt -:AntPosition-> n:GridNode\AntHill;
    negative { 
        n <-:PathToHill-;
    }
    return (n);
}
```

# Example: blockchain contract languages
https://blog.chain.com/announcing-ivy-playground-395364675d0a

```
contract CallOption(
  strikePrice: Amount,
  strikeCurrency: Asset,
  seller: Program,
  buyerKey: PublicKey,
  expiration: Time
) locks underlying {
  clause exercise(
    buyerSig: Signature
  ) requires payment: strikePrice of strikeCurrency {
    verify before(expiration)
    verify checkTxSig(buyerKey, buyerSig)
    lock payment with seller
    unlock underlying
  }
  clause expire() {
    verify after(expiration)
    lock underlying with seller
  }
}
```

# Blockchain contract languages {.twocolumn}
http://solidity.readthedocs.io/en/latest/

```
pragma solidity ^0.4.11;

contract SimpleAuction {
    address public beneficiary;
    uint public auctionStart;
    uint public biddingTime;

    address public highestBidder;
    uint public highestBid;

    mapping(address => uint) pendingReturns;

    bool ended;

    event HighestBidIncreased(address bidder, uint amount);
    event AuctionEnded(address winner, uint amount);

    function SimpleAuction(
        uint _biddingTime,
        address _beneficiary
    ) {
        beneficiary = _beneficiary;
        auctionStart = now;
        biddingTime = _biddingTime;
    }

    function bid() payable {
        require(now <= (auctionStart + biddingTime));

        require(msg.value > highestBid);

        if (highestBidder != 0) {
            pendingReturns[highestBidder] += highestBid;
        }
        highestBidder = msg.sender;
        highestBid = msg.value;
        HighestBidIncreased(msg.sender, msg.value);
    }

    function withdraw() returns (bool) {
        var amount = pendingReturns[msg.sender];
        if (amount > 0) {
            pendingReturns[msg.sender] = 0;

            if (!msg.sender.send(amount)) {
                pendingReturns[msg.sender] = amount;
                return false;
            }
        }
        return true;
    }

    function auctionEnd() {
        require(now >= (auctionStart + biddingTime)); // auction did not yet end
        require(!ended); // this function has already been called

        ended = true;
        AuctionEnded(highestBidder, highestBid);

        beneficiary.transfer(highestBid);
    }
}
```

# Embedded and external DSLs

External DSL (all previous examples) — programming language with its own syntax and tools (translator).

Embedded DSL (internal DSL, following examples) uses facilities of some general-purpose host language.

## LINQ
Domain: declarative relational queries

<div class="twocolumn">

```csharp
using (ServiceContext svcContext = new ServiceContext(_serviceProxy))
{
 var query_join4 = from a in svcContext.AccountSet
                   join c in svcContext.ContactSet
                   on a.PrimaryContactId.Id equals c.ContactId
                   join l in svcContext.LeadSet
                   on a.OriginatingLeadId.Id equals l.LeadId
                   select new
                   {
                    contact_name = c.FullName,
                    account_name = a.Name,
                    lead_name = l.FullName
                   };
 foreach (var c in query_join4)
 {
  System.Console.WriteLine(c.contact_name +
   "  " +
   c.account_name +
   "  " +
   c.lead_name);
 }
}
```

Query is translated into something like this:

```csharp
svcContext.AccountSet.All()
    .Join(svcContext.ContactSet, (a, c) => a.PrimaryContactId.Id == c.ContactId)
    .Join(svcContext.LeadSet, (a, c, l) => a.OriginatingLeadId.Id == l.LeadId)
    .Select((a, c, l) => new AnonymousStruct123 {
        contact_name = c.FullName,
        account_name = a.Name,
        lead_name = l.FullName
    })
```
<div>

# Example: Haskell eDSLs
https://github.com/bitemyapp/esqueleto

All "keywords" and "operators" are ordinary functions in Haskell (except `do`, `$` and lambda-abstraction)
```haskell
let complexQuery =
    from $ \(p1 `InnerJoin` p2) -> do
    on (p1 ^. PersonName ==. p2 ^. PersonName)
    where_ (p1 ^. PersonFavNum >. val 2)
    orderBy [desc (p2 ^. PersonAge)]
    limit 3
    offset 9
    groupBy (p1 ^. PersonId)
    having (countRows <. val (0 :: Int))
    return (p1, p2)
```

# Example: Ruby eDSLs
http://squib.rocks/

# Example: Lisp DSLs
http://swizard.info/articles/solitaire/article.html

```cl
;; Define cards
(slt/defcards (suites (S C H D))
              (ranks (A 2 3 4 5 6 7 8 9 T J Q K))
              (colors ((black (S C)) (red (H D)))))


;; Define card piles 
(slt/defpiles (stock (24))
              (waste (0))
              (foundation (0 0 0 0))
              (tableau (1 2 3 4 5 6 7)))

;; Reveal stock -> waste
(slt/defrule ((pile s stock) (pile w waste) (top-card c (on s) unknown))
  (reveal-top s)
  (move-top s w))

;; Reveal tableau
(slt/defrule ((pile tb tableau) (top-card c (on tb) unknown))
  (reveal-top tb))

;; Move King from waste to empty tableau
(slt/defrule ((pile w waste) (pile tb tableau) (top-card cw (on w) K) (top-card ct (on tb) empty))
  (move-top w tb))
```

# Language-oriented programming
Ward, MP. “Language-Oriented Programming.” Software - Concepts and Tools 15, no. 4 (1994): 147–61.
http://www.cse.dmu.ac.uk/~mward/martin/papers/middle-out-t.pdf

This paper describes the concept of language oriented programming which is a novel way of
organising the development of a large software system, leading to a different structure for the
finished product. The approach starts by developing a formally specified, domain-oriented, very
high-level language which is designed to be well-suited to developing “this kind of program”.
The development process then splits into two independent stages: (1) Implement the system
using this “middle level” language, and (2) Implement a compiler or translator or interpreter
for the language, using existing technology. 

http://www.onboard.jetbrains.com/articles/04/10/lop/
https://www.martinfowler.com/articles/languageWorkbench.html

# Example: JetBrains MPS
https://www.jetbrains.com/mps/
https://confluence.jetbrains.com/display/MPSD20171/Shapes+-+an+introductory+MPS+tutorial

# Language composition

- some syntax subtrees may be common for different languages, for example, boolean and arithmetical expressions
- we can decompose some existing language to a separate parts, for example in CSS there is a selector syntax and property syntax:

```css
form > label > .redtext {
    color: red;
}

.redtext:hover {
    border: solid green 1px;
}
```
selectors language is reused in JavaScript (in jQuery library and document.querySelector function)

- some well-designed programming libraries usually are some kind of embedded DSLs

```python
from django.db.models import F, Sum, Value as V
from django.db.models.functions import Coalesce

InvoiceItem.objects.values('id', 'amount').annotate(
    to_pay=F('amount') - Coalesce(Sum('paymentdistribution__amount'), V(0))
).order_by('-to_pay')
```

- problems: syntax composition (how to write parser which can detect language boundaries), language interoperability

# Towards intermediate representation languages

- language-oriented approach presumes the extensive usage of different DSLs
- problem 1: how to write the effecient translators for these languages?
- problem 2: how do languages interoperate with each other?

# Intermediate representation

Intermediate representation programming language is a special case of programming language which serves as an interface between some stages of program translation. Intermediate representations usually are not used by humans, have no concrete syntax (except for the purposes of debugging).

- DSLs interoperability can be represented in terms of a common intermediate representation
- common intermediate representation may have formal specification (in this case the semantics of DSL is specified in terms of translation)

# Classification
(my classification, paper is in print)
## Low-level intermediate representations
## Bytecode for imperative languages
## Bytecode for functional languages
## Typed control flow graphs
## High-level language-specific representations 

# Low-level intermediate representations
- linear code structure
- data types are specified in terms of memory layout
- instruction set is close to machine/assembly language

# Low-level intermediate representation
- three-address code, abstract representation, see "Dragon book" (Aho, Seti, Ullman "Compilers: Principles, Techniques, and Tools")
    - each instruction has opcode and at most 3 operands ("addresses")
- RTL (register transfer language), abstract representation
    - GCC uses RTL

- SSA (static single assignment form), abstract representation
  - each memory cell state is enumerated, conditional operators use special "phi-function" to determine what state to assign after branching
  - LLVM is based on SSA
    - http://llvm.org/
    - http://www.cis.upenn.edu/~stevez/vellvm/

- TAL (typed assembly language) is formally specified low-level intermediate representation,
    code blocks are represented by triples (H — heap state, R — register state, I — instruction sequence)
    Morrisett G. et al. From System F to Typed Assembly Language // ACM Trans. Program. Lang. Syst. 1999. Т. 21, № 3. Сс. 527–568.

# Bytecode application virtual machines
- linear code structure
- high-level instructions
- high-level (usually memory layout-independent) date type representation
- typing, first-order polymorphism

# Bytecode for imperative languages
- CLI/JVM
    ISO. ISO/IEC 23271:2003: Information technology — Common Language Infrastructure. 

    Examples of high-level instruction: `ldfld`, `callvirt`, `isinstance`

- Vortex IL
    Dean J. et al. Vortex: An Optimizing Compiler for Object-oriented Languages // Proceedings of the 11th ACM SIGPLAN Conference on Object-oriented Programming, Systems, Languages, and Applications. New York, NY, USA: ACM, 1996. Сс. 83–100.

```
class Square isa Rhombus, Quadrilateral;
method draw__6Square_int(this, color):void;
associate draw__6Square_int with draw__5Shape_int(@Square, @any)

send draw__5Shape_int(s, color)
```

# Bytecode for functional languages
- usually untyped
- supports closure creation at the level of bytecode

- ZINC — intermediate representation for ML-family languages
    Leroy X. The ZINC experiment: an economical implementation of the ML language: Technical report. INRIA, 1990.

- Python bytecode
    https://laanwj.github.io/2011/5/4/python-bytecode-archeology
    example instructions: LOAD_CLOSURE, MAP_ADD (add item to dictionary)

- Warren's Abstract Machine (for Prolog language)
    Aït-Kaci H. Warren’s Abstract Machine: A Tutorial Reconstruction. Cambridge, Mass: The MIT Press, 1991. 114 с.
    example instructions: `try_me_else` (representation of choice list), `unify_constant`

    ```
     girl(sally).
     girl(jane).
 
     boy(B) :- \+ girl(B).
    ```

    ```
    predicate(girl/1):
        switch_on_term(2,1,fail,fail,fail),
    label(1): switch_on_atom([(sally,3),(jane,5)])
    label(2): try_me_else(4)
    label(3): get_atom(sally,0)
            proceed
    label(4): trust_me_else_fail
    label(5): get_atom(jane,0)
            proceed
    
    predicate(boy/1):
        get_variable(x(1),0)
        put_structure(girl/1,0)
        unify_local_value(x(1))
        execute((\+)/1)])
    ```

# Typed control flow graphss

- graph-based code structure (special constructions at join points are used instead of labels and jump instructions)
Leißa R., Köster M., Hack S. A graph-based higher-order intermediate representation // 2015 IEEE/ACM International Symposium on Code Generation and Optimization (CGO). 2015. Сс. 202–212.

# High-level language-specific representations 
- Clight in CompCert (previously discussed)
- LISP (and, less frequently, Forth) also can be used as high-level general-purpose representations

# Nanopass compiler
D. Sarkar, O. Waddell, and R. K. Dybvig. A nanopass infrastructure for compiler education. In ICFP ’04: Proceedings of the ninth ACM SIGPLAN International Conference on Functional Programming, pages 201–212, New York, NY, USA, 2004. ACM.

Translation pipeline is specified in terms of ~50 simple transformation steps between languages. Nanopass framework provides the specification DSL for languages. Languages may be specified in terms of modification of output languages from previous pass.

 
