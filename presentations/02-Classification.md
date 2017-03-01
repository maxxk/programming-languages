# Software and Programming Language Theory
## Classification of programming languages
### Characteristics of programming languages
### (How do programming languages differ from each other)
<style>
.twocolumn {
  -moz-column-count: 2;
  -webkit-column-count: 2;
}
.small { font-size: small !important; }
.smaller { font-size: 0.8em !important; }


</style>

Course page: https://maxxk.github.io/programming-languages-2016/
<span style="font-size: small">Chrome or Firefox are recommended to watch presentations; Konqueror in aud. 13-15 skips some slides. Open with Firefox and enable scripts with button “Settings”, lower right corner</span>

# Literature
1. B. Pierce. Types and Programming Languages. MIT Press. 2002.
2. D. Watt. Programming Language Design Concepts. Wiley. 2004.
3. F. Turbak, D. Gifford. Design Concepts in Programming Languages. 2008.
4. R. Sebesta. Concepts of Programming Languages. 2012.

# History of programming languages

# Forth: stack-based and concatenative
Uses point-free notation (e.g. f ∘ g instead of λx. f(g(x))).
```factor
: reshape ( width height -- )
    [ 0 0 ] 2dip glViewport
    GL_PROJECTION glMatrixMode
    glLoadIdentity
    -30.0 30.0 -30.0 30.0 -30.0 30.0 glOrtho
    GL_MODELVIEW glMatrixMode ;

: paint ( -- )
    0.3 0.3 0.3 0.0 glClearColor
    GL_COLOR_BUFFER_BIT GL_DEPTH_BUFFER_BIT bitor glClear
    GL_SMOOTH glShadeModel
    glLoadIdentity
    -15.0 -15.0 0.0 glTranslatef
    GL_TRIANGLES glBegin
    1.0 0.0 0.0 glColor3f 0.0 0.0 glVertex2f
    0.0 1.0 0.0 glColor3f 30.0 0.0 glVertex2f
    0.0 0.0 1.0 glColor3f 0.0 30.0 glVertex2f
    glEnd
    glFlush ;

M: triangle-gadget pref-dim* drop { 640 480 } ;
M: triangle-gadget draw-gadget*
    rect-bounds nip first2 reshape paint ;

: triangle-window ( -- )
   [ triangle-gadget new "Triangle" open-window ] with-ui ;
MAIN: triangle-window
```


# Ada programming language
<span class="small">Missing parts from previous lecture. Based on «R. Sebesta. Concepts of Programming Lanugages. 2012»</span>
US Department of Defense initiated the design process of programming language in 1975. Original motivation: make a single high-level programming language for a broad range of applications, including critical embedded systems. At the time of proposal, about 450 different languages was in use by DoD contractors.

In 1979 design and rationale of Ada were published in ACM SIGPLAN Notices. In October 1979 a public test and evaluation conference was held. In the conference representatives from over 100 US and Europe organizations took part.

Final specification was mostly completed in 1980, revised and published in 1983. First usable compilers appeared around 1980.

Revised in 1995, 2005 (both revisions featuring OOP and concurrency enhancements). Widely used in aviation.

# Ada
```ada
procedure Ada_Ex is
  type Int_List_Type is array (1..99) of Integer; Int_List : Int_List_Type;
  List_Len, Sum, Average, Result : Integer;
begin
  Result:= 0;
  Sum := 0;
  Get (List_Len);
  if (List_Len > 0) and (List_Len < 100) then
  -- Read input data into an array and compute the sum
  for Counter := 1 .. List_Len loop
      Get (Int_List(Counter));
      Sum := Sum + Int_List(Counter);
  end loop;
  -- Compute the average
  Average := Sum / List_Len;
  -- Count the number of values that are > average
  for Counter := 1 .. List_Len loop
      if Int_List(Counter) > Average then
        Result:= Result+ 1;
      end if;
  end loop;
  -- Print result
  Put ("The number of values > average is:");
  Put (Result);
  New_Line;
else
    Put_Line ("Error—input list length is not legal");
end if;
end Ada_Ex;
```

# Ada
```ada
generic
  capacity: Positive;
package Queues is
  type Queue is limited private;
  -- A Queue value represents a queue whose elements are characters and
  -- whose maximum length is capacity.
  procedure clear (q: out Queue);
  -- Make queue q empty.
  procedure add (q: in out Queue; e: in Character);
  -- Add element e to the rear of queue q.
  procedure remove (q: in out Queue; e: out Character);
  -- Remove element e from the front of queue q. private
  type Queue is record
    length: Integer range 0 .. capacity;
    front, rear: Integer range 0 .. capacity-1; elems: array (0 .. capacity-1) of Character;
  end record;
  -- A queue is represented by a cyclic array, with the queued elements
  -- stored either in elems(front..rear-1) or in
  -- elems(front..capacity-1) and elems(0..rear-1).
end Queues;
```


# Characteristics of programming languages
How the modern programming languages differ from each other?

Two major categories:
- «internal» characteristic — what is representable in the language, features of any language implementation
E.g.: static vs dynamic typing
- «external» characteristic — features of specific language implementation
E.g. interpreted vs compiled

# Interpreted vs compiled: Python
- [CPython](https://www.python.org/) — interpreter (reference implementation)
- [PyPy](http://pypy.org/), [IronPython](http://ironpython.net/), [Jython](https://wiki.python.org/jython/) — just-in-time compiler (specific tracing JIT, .NET, JVM, respectively)
- [Cython](http://cython.org/), [Nuitka](http://nuitka.net/) — ahead-of-time compiler

# Imperative programming
Main entities:
- variable (abstracts memory block and register loading)
- command (operation on variables)
- procedure (sequence of commands with inputs and outputs)

# Object-oriented programming
Main entities:
- object
- class - a family of similar objects

Classical concepts:
- encapsulation - hiding of irrelevant implementation details (e.g. fields)
- inheritance - a subclass inherits all methods of superclass, unless subclass explicitly overrides a method
- inclusion polymorphism (Liskov substitution principle) - objects of subclasses can be treated uniformly as an object of common superclass

# Prototype-based object-oriented programming
The concept of class is implemented by the means of a prototype object, which defines the common methods for objects.
Well-known implementation: JavaScript (ECMAScript)

```javascript
[1, 2, 3].sum();
// ⟶ Exception: sum is not defined

Array.prototype.sum = function() {
  let sum = 0;
  for (let i = 0; i < this.length; i++) {
    sum += this[i];
  }
  return sum;
}

[1, 2, 3].sum();
// ⟶ 6
```

# Live programming
## (interactive programming)
Program components are written in the same environment as the program runs.
Original idea comes from Alan Kay's Ph.D. thesis «The Reactive Engine» (1969), which describes the live programming computer system. Smalltalk was the successor of this idea.

Main example today: web browser.
```javascript
document.querySelector('#live-programming h1')
  .addEventListener('click', function(e) {
    e.target.style.fontSize=Math.random()*70 + 'pt';
  })
```

# Type system

> A type system is a tractable syntactic method for proving the absence of certain program behaviors by classifying phrases according to the kinds of values they compute.
> <span class="small">B. Pierce. Types and Programming Languages. 2002.</span>

- safe and unsafe
- statically-checked and dynamically-checked

# Dynamic typechecking
```javascript
function dynamic(x) {
  return x + 1;
}

x(1) // ⟶ 2

x('1') // ⟶ '11'
// (in some languages it returns 2)

x(['a', 'b']) // ⟶ 'a,b1'
// (in some languages it may return ['a', 'b', 1])
```

# Optional type systems
It is possible to provide external static type checking engine for dynamic language. It works as a statical analysis tool. Proper type systems for dynamic language are rather complex (it usually employs row-types).
Examples:
- [Typed Clojure](http://typedclojure.org)
- [mypy (Python)](https://github.com/python/mypy)
- [**Flow (Javascript, by Facebook)**](http://flowtype.org)

```javascript
type BinaryTree =
  { kind: "leaf", value: number } |
  { kind: "branch", left: BinaryTree, right: BinaryTree }

function sumLeaves(tree: BinaryTree): number {
  if (tree.kind === "leaf") {
    return tree.value;
  } else {
    return sumLeaves(tree.left) + sumLeaves(tree.right);
  }
}
```

# Optional typing
Usually external type checkers allow to use annotations as special comments (remember the model checking from the previous semester).
```javascript
/*::
type BinaryTree =
  { kind: "leaf", value: number } |
  { kind: "branch", left: BinaryTree, right: BinaryTree }
*/

function sumLeaves(tree /*: BinaryTree*/) /*: number*/ {
  if (tree.kind === "leaf") {
    return tree.value;
  } else {
    return sumLeaves(tree.left) + sumLeaves(tree.right);
  }
}
```

# Row types
The data type which describes required fields (or methods) in data structure, but allows a programmer to extend fields.
```javascript
type BinaryTree =
  { kind: "leaf", value: number } |
  { kind: "branch", left: BinaryTree, right: BinaryTree }

{ kind: "leaf", value: 1, additional: "Hey!" } /*: BinaryTree */
```

# Structural typing
## Duck typing
> When I see a bird that walks like a duck and swims like a duck and quacks like a duck, I can call that bird a duck.
> <span class="small">J. Riley</span>

```javascript
function addAB(x /*: { a: number, b: number } */) /*: number */ {
  return x.a + x.b;
}
```

# Functional programming
Primary entities:
- expression (way of compute new values from old)
- function (a composition of expressions)
- parametric polymorphism (means of making the same operations on different variables)

Programs are created by composing functions and usually by avoiding *mutable state*.

# Currying and partial application
People say: language support functional programming if the functions are the first-class citizens.
The core point of this definition can be reduced to implementability of two operations: currying and partial application.

```c
double fmax(double a, double b);
```

Curry: make from two-argument function a function which returns an another function.
fmax : (a : double) × (b : double) → double
curry(fmax) : (a : double) → ((b : double) → double)

Partial application:
curry(fmax) · 0 : (b : double) → double

```javascript
function fmax(a, b) { return a < b ? b : a }
function curry2(func) { return function(x) { return function(y) { return func(x, y) } } }
const curried_fmax = curry2(fmax);
const nonnegative = curried_fmax(0);
Math.sqrt(nonnegative(z));
```

# Pure functional programming
- **Pure function** — a function for which the output depends only on the values of its arguments (no global mutable state).

```javascript
function pure(x) {
  return x+1;
}

pure(1) /* ⟶ 2 */
pure(1) /* ⟶ 2 */

Array.prototype.impure = function(x) {
  this.push(x); /* mutation */
  return this.length;
}

var x = [];
x.impure(1) /* ⟶ 1 */
x.impure(1) /* ⟶ 2 */
```

# Pure functional programming
Example: Haskell
Advantage: an order of computation is insignificant. An optimizing compiler may translate functional program by taking advantage of mutable state.

**Tail recursion**

```javascript
function factorial_(n, accumulator) {
  return n == 0 ? accumulator : factorial_(n-1, accumulator);
}
function factorial(x) { return factorial_(x, 1) }
```

. . .

```javascript
/* Is translated to: */
function factorial_(n, accumulator) {
START: do {
    if (n == 0) return accumulator;
    accumulator *= n;
    n -= 1;
    continue START;
  } while(0)
}
```

# Polymorphism
- a means of making the same operations for different arguments.

## Ad-hoc polymorphism
## Subtype polymorphism
(polymorphism in OOP sense)
## Parametric polymorphism
## Higher-order polymorphism

# Ad-hoc polymorphism
Function (operator) overloading — the same name for different types corresponds to the different implementations. Example: C++

```c++
int volume(int s)
{
    return s*s*s;
}

// volume of a cylinder
double volume(double r, int h)
{
    return 3.14*r*r*static_cast<double>(h);
}
// volume of a cuboid
long volume(long l, int b, int h)
{
    return l*b*h;
}
```

# Subtype polymorphism
The same function for subtype may call different implementations
```java
abstract class Animal {
    abstract String talk();
}

class Cat extends Animal {
    String talk() {
        return "Meow!";
    }
}

class Dog extends Animal {
    String talk() {
        return "Woof!";
    }
}
```

# Parametric polymorphism
Some *generic* parameters may be substituted to the function which describe the types of the subprogram parameters.

```java
public class MyClass<T, U, V, W>
    where T : class,        // T should be a reference type (array, class, delegate, interface)
        new()               // T should have a public constructor with no parameters
    where U : struct        // U should be a value type (byte, double, float, int, long, struct, uint, etc.)
    where V : MyOtherClass, // V should be derived from MyOtherClass
        IEnumerable<U>      // V should implement IEnumerable<U>
    where W : T,            // W should be derived from T
        IDisposable         // W should implement IDisposable
{
    public MyClass(U u, V v, W w) {
      var t = new T();
      var u2 = u; // make a copy (value-type)
      foreach (var e in v) {
        /* ... */
      }
      w.Dispose();
    }
}
```

# Higher order polymorphism
The generic parameters can be parametrized as well by some means of type-level computation. Example: functor.
Reminder: a functor $F$ is a pair of an object-map $$ F : A → B $$ and an arrow-map $$ fmap : (a_1 → a_2) → (F(a_1) → F(a_2))$$

```java
/* not actually possible in Java or C# */
interface Functor<F<*>> {
  F<B> fmap<A, B>(F<A> a, Function<A, B> f);
}
```

```haskell
class  Functor f  where
    fmap :: (a -> b) -> f a -> f b

-- elementwise map is a functor on lists
instance  Functor []  where
    fmap = map
```

# Evaluation strategy
## Strict vs Non-strict evaluation
Strict (eager) evaluation: all function arguments are evaluated before call.

Non-strict (lazy) evaluation: function arguments are evaluated only when used.

```javascript
function f(a, b, c) { return a+b; }
function ⟂() { while(true) {} }
f(1, 2, ⟂())
// Strict evaluation ⟶ ⟂
// Non-strict evaluation ⟶ 3
```

# Strict evaluation

`void example(int a, int* b, vector<int> v)`

call-by-value
~ (like in C for non-pointer arguments) arguments are copied to the callee memory, callee-made changes are *not visible* to caller

call-by-reference
~ (like pointer arguments in C or reference arguments in C++) callee gets references to the arguments, callee-made changes are *visible* to caller

call-by-sharing
~ arguments are placed on the shared heap, they can't be reassigned, but can be changed

# Lazy evaluation
call-by-name
~ (Algol-60) arguments are directly substituted to caller code (as a terms in lambda-calculus)

call-by-need
~ (Haskell) the result of argument computation is cached (so that the two different substitutions of the same argument share the same result)

# Macro expansion
call-by-macro-expansion
~ (TeX, C preprocessor) substitute the macros with its body it the source code, possibly capturing identifiers.

```c
#define MACRO(a) (a < b)

int f(int b) {
  return MACRO(0);
}

int g(int a) {
  return MACRO(0) // error: b not defined
}
```

# Hygienic macros
- macro-system which prevents (accidential) capture of identifier.

```nemerle
macro ReverseFor (i, begin, body)
syntax ("for_", "(", begin, ")", body)
{
  <[ for (int i = $begin; $i >= 0; i--) $body ]>
}
```

```nemerle
for_ (n) print(i); // Error: i is not defined
```

# Homework assignments

**Task 2.1***  Select 5 [random](https://www.random.org/integer-sets/) programming languages (e.g. from [GitHub highlighting repository](https://github.com/github/linguist/tree/master/vendor/grammars) or from [Wikipedia](https://en.wikipedia.org/wiki/List_of_programming_languages)). Describe each of them in terms of features you learned today and at the next lectures (~1 page, language primary focus, typing system, main features, reference implementation features).
