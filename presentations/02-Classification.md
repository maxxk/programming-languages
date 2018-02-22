# Software and Programming Language Theory
## Classification of programming languages
### Characteristics of programming languages
### (How do programming languages differ from each other)

<div style="text-align: center">
https://maxxk.github.io/programming-languages/

<span style="font-size: small">Chrome or Firefox are recommended to watch presentations; Konqueror in aud. 13-16 skips some slides. Open with Firefox and enable scripts with button “Settings”, lower right corner</span>
</div>

# Literature
1. B. Pierce. Types and Programming Languages. MIT Press. 2002.
2. D. Watt. Programming Language Design Concepts. Wiley. 2004.
3. F. Turbak, D. Gifford. Design Concepts in Programming Languages. 2008.
4. R. Sebesta. Concepts of Programming Languages. 2012.

# History of programming languages (additions)

# Forth: stack-based and concatenative
Uses point-free notation (e.g. f ∘ g instead of λx. f(g(x))).
```forth
: FILL   FAUCETS OPEN  TILL-FULL  FAUCETS CLOSE ;
: RINSE   FILL AGITATE DRAIN ; 
: WASHER   WASH SPIN RINSE SPIN ;
```


# Ada programming language
<span class="small">Missing parts from previous lecture. Based on «R. Sebesta. Concepts of Programming Lanugages. 2012»</span>
US Department of Defense initiated the design process of programming language in 1975. Original motivation: make a single high-level programming language for a broad range of applications, including critical embedded systems. At the time of proposal, about 450 different languages was in use by DoD contractors.

In 1979 design and rationale of Ada were published in ACM SIGPLAN Notices. In October 1979 a public test and evaluation conference was held. In the conference representatives from over 100 US and Europe organizations took part.

Final specification was mostly completed in 1980, revised and published in 1983. First usable compilers appeared around 1980.

Revised in 1995, 2005 (both revisions featuring OOP and concurrency enhancements). Widely used in avionics.

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

E.g. static vs dynamic typing

- «external» characteristic — features of specific language implementation

E.g. interpreted vs compiled

# Interpreted vs compiled: Python
- [CPython](https://www.python.org/) — interpreter (reference implementation)
- [PyPy](http://pypy.org/), [IronPython](http://ironpython.net/), [Jython](https://wiki.python.org/jython/) — just-in-time compiler (tracing JIT, .NET, JVM, respectively)
- [Cython](http://cython.org/), [Nuitka](http://nuitka.net/) — ahead-of-time compiler (to machine code)

# Imperative programming
Main entities:
- variable (abstracts memory block and register loading)
- command (operation on variables)
- procedure (sequence of commands with inputs and outputs)

# Object-oriented programming
## Main entities

object
~ a main concept of OOP, may contain data (fields) and code (methods)

class
~ a family of similar objects

## Classical concepts

encapsulation
~ hiding of irrelevant implementation details (e.g. fields)

inheritance
~ a subclass inherits all methods of superclass, unless subclass explicitly overrides a method

inclusion polymorphism (Liskov substitution principle)
~ objects of subclasses can be treated uniformly as an object of common superclass


# Prototype-based object-oriented programming
The concept of class is implemented by the means of a prototype object, which defines the common methods for objects.
Well-known implementation: JavaScript (ECMAScript)

```javascript
var a = [1, 2, 3];

a.sum();
// ⟶ Exception: sum is not defined

Array.prototype.sum = function() {
  let sum = 0;
  for (let i = 0; i < this.length; i++) {
    sum += this[i];
  }
  return sum;
}

a.sum();
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

# Dynamic typechecking and weak typing
```javascript
function dynamic(x) {
  return x + 1;
}

dynamic(1) // ⟶ 2

dynamic('1') // ⟶ '11'
// (in some languages it returns 2)

dynamic(['a', 'b']) // ⟶ 'a,b1'
// (in some languages it may return ['a', 'b', 1])

dynamic({})
```

# Optional type systems
It is possible to provide external static type checking engine for dynamic language. It works as a statical analysis tool. Proper type systems for dynamic language are rather complex (it usually employs row-types).
Examples:
- [Typed Clojure](http://typedclojure.org)
- [mypy (Python)](https://github.com/python/mypy)
- [**Flow (JavaScript, by Facebook)**](http://flowtype.org)
- [Typescript (JavaScript, by Microsoft)](http://typescriptlang.org)

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
Math.sqrt(nonnegative(z)); // = Math.sqrt(z < 0 ? 0 : z)
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
  return n == 0 ? accumulator : factorial_(n-1, n*accumulator);
}
function factorial(x) { return factorial_(x, 1) }
```

is translated to:

```javascript
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
— a means of making the same operations for different arguments.

## Ad-hoc polymorphism
## Subtype polymorphism
(polymorphism in OOP sense)
## Dynamic dispatch
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

# Dynamic dispatch
— an process of selecting which implementation of a polymorphic operation to call at run time. 

single dispatch
~ the decision is based on a single argument (this is how the subtype polymorphism works)

multiple dispatch
~ the decision is based on multiple arguments

Example of multiple dispatch: an implementation of the addition operator in [Julia programming language](http://docs.julialang.org/en/release-0.4/manual/methods/):

<div style="overflow-y: scroll; height: 10em;" >

```julia
julia> methods(+)
# 139 methods for generic function "+":
+(x::Bool) at bool.jl:33
+(x::Bool,y::Bool) at bool.jl:36
+(y::AbstractFloat,x::Bool) at bool.jl:46
+(x::Int64,y::Int64) at int.jl:14
+(x::Int8,y::Int8) at int.jl:14
+(x::UInt8,y::UInt8) at int.jl:14
+(x::Int16,y::Int16) at int.jl:14
+(x::UInt16,y::UInt16) at int.jl:14
+(x::Int32,y::Int32) at int.jl:14
+(x::UInt32,y::UInt32) at int.jl:14
+(x::UInt64,y::UInt64) at int.jl:14
+(x::Int128,y::Int128) at int.jl:14
+(x::UInt128,y::UInt128) at int.jl:14
+(x::Float32,y::Float32) at float.jl:192
+(x::Float64,y::Float64) at float.jl:193
+(z::Complex{T<:Real},w::Complex{T<:Real}) at complex.jl:96
+(x::Real,z::Complex{T<:Real}) at complex.jl:106
+(z::Complex{T<:Real},x::Real) at complex.jl:107
+(x::Rational{T<:Integer},y::Rational{T<:Integer}) at rational.jl:167
+(a::Float16,b::Float16) at float16.jl:136
+(x::Base.GMP.BigInt,y::Base.GMP.BigInt) at gmp.jl:243
+(a::Base.GMP.BigInt,b::Base.GMP.BigInt,c::Base.GMP.BigInt) at gmp.jl:266
+(a::Base.GMP.BigInt,b::Base.GMP.BigInt,c::Base.GMP.BigInt,d::Base.GMP.BigInt) at gmp.jl:272
+(a::Base.GMP.BigInt,b::Base.GMP.BigInt,c::Base.GMP.BigInt,d::Base.GMP.BigInt,e::Base.GMP.BigInt) at gmp.jl:279
+(x::Base.GMP.BigInt,c::Union{UInt32,UInt16,UInt8,UInt64}) at gmp.jl:291
+(c::Union{UInt32,UInt16,UInt8,UInt64},x::Base.GMP.BigInt) at gmp.jl:295
+(x::Base.GMP.BigInt,c::Union{Int16,Int32,Int8,Int64}) at gmp.jl:307
+(c::Union{Int16,Int32,Int8,Int64},x::Base.GMP.BigInt) at gmp.jl:308
+(x::Base.MPFR.BigFloat,y::Base.MPFR.BigFloat) at mpfr.jl:206
+(x::Base.MPFR.BigFloat,c::Union{UInt32,UInt16,UInt8,UInt64}) at mpfr.jl:213
+(c::Union{UInt32,UInt16,UInt8,UInt64},x::Base.MPFR.BigFloat) at mpfr.jl:217
+(x::Base.MPFR.BigFloat,c::Union{Int16,Int32,Int8,Int64}) at mpfr.jl:221
+(c::Union{Int16,Int32,Int8,Int64},x::Base.MPFR.BigFloat) at mpfr.jl:225
+(x::Base.MPFR.BigFloat,c::Union{Float16,Float64,Float32}) at mpfr.jl:229
+(c::Union{Float16,Float64,Float32},x::Base.MPFR.BigFloat) at mpfr.jl:233
+(x::Base.MPFR.BigFloat,c::Base.GMP.BigInt) at mpfr.jl:237
+(c::Base.GMP.BigInt,x::Base.MPFR.BigFloat) at mpfr.jl:241
+(a::Base.MPFR.BigFloat,b::Base.MPFR.BigFloat,c::Base.MPFR.BigFloat) at mpfr.jl:318
+(a::Base.MPFR.BigFloat,b::Base.MPFR.BigFloat,c::Base.MPFR.BigFloat,d::Base.MPFR.BigFloat) at mpfr.jl:324
+(a::Base.MPFR.BigFloat,b::Base.MPFR.BigFloat,c::Base.MPFR.BigFloat,d::Base.MPFR.BigFloat,e::Base.MPFR.BigFloat) at mpfr.jl:331
+(x::Irrational{sym},y::Irrational{sym}) at constants.jl:71
+{T<:Number}(x::T<:Number,y::T<:Number) at promotion.jl:205
+{T<:AbstractFloat}(x::Bool,y::T<:AbstractFloat) at bool.jl:43
+(x::Number,y::Number) at promotion.jl:167
+(x::Integer,y::Ptr{T}) at pointer.jl:70
+(x::Bool,A::AbstractArray{Bool,N}) at array.jl:829
+(x::Integer,y::Char) at char.jl:41
+(x::Number) at operators.jl:72
+(r1::OrdinalRange{T,S},r2::OrdinalRange{T,S}) at operators.jl:325
+{T<:AbstractFloat}(r1::FloatRange{T<:AbstractFloat},r2::FloatRange{T<:AbstractFloat}) at operators.jl:331
+(r1::FloatRange{T<:AbstractFloat},r2::FloatRange{T<:AbstractFloat}) at operators.jl:348
+(r1::FloatRange{T<:AbstractFloat},r2::OrdinalRange{T,S}) at operators.jl:349
+(r1::OrdinalRange{T,S},r2::FloatRange{T<:AbstractFloat}) at operators.jl:350
+(x::Ptr{T},y::Integer) at pointer.jl:68
+{S,T}(A::Range{S},B::Range{T}) at array.jl:773
+{S,T}(A::Range{S},B::AbstractArray{T,N}) at array.jl:791
+(A::AbstractArray{Bool,N},x::Bool) at array.jl:828
+(A::BitArray{N},B::BitArray{N}) at bitarray.jl:926
+(A::Union{DenseArray{Bool,N},SubArray{Bool,N,A<:DenseArray{T,N},I<:Tuple{Vararg{Union{Colon,Range{Int64},Int64}}},LD}},B::Union{DenseArray{Bool,N},SubArray{Bool,N,A<:DenseArray{T,N},I<:Tuple{Vararg{Union{Colon,Range{Int64},Int64}}},LD}}) at array.jl:859
+(A::Base.LinAlg.SymTridiagonal{T},B::Base.LinAlg.SymTridiagonal{T}) at linalg/tridiag.jl:59
+(A::Base.LinAlg.Tridiagonal{T},B::Base.LinAlg.Tridiagonal{T}) at linalg/tridiag.jl:254
+(A::Base.LinAlg.Tridiagonal{T},B::Base.LinAlg.SymTridiagonal{T}) at linalg/special.jl:113
+(A::Base.LinAlg.SymTridiagonal{T},B::Base.LinAlg.Tridiagonal{T}) at linalg/special.jl:112
+(A::Base.LinAlg.UpperTriangular{T,S<:AbstractArray{T,2}},B::Base.LinAlg.UpperTriangular{T,S<:AbstractArray{T,2}}) at linalg/triangular.jl:164
+(A::Base.LinAlg.LowerTriangular{T,S<:AbstractArray{T,2}},B::Base.LinAlg.LowerTriangular{T,S<:AbstractArray{T,2}}) at linalg/triangular.jl:165
+(A::Base.LinAlg.UpperTriangular{T,S<:AbstractArray{T,2}},B::Base.LinAlg.UnitUpperTriangular{T,S<:AbstractArray{T,2}}) at linalg/triangular.jl:166
+(A::Base.LinAlg.LowerTriangular{T,S<:AbstractArray{T,2}},B::Base.LinAlg.UnitLowerTriangular{T,S<:AbstractArray{T,2}}) at linalg/triangular.jl:167
+(A::Base.LinAlg.UnitUpperTriangular{T,S<:AbstractArray{T,2}},B::Base.LinAlg.UpperTriangular{T,S<:AbstractArray{T,2}}) at linalg/triangular.jl:168
+(A::Base.LinAlg.UnitLowerTriangular{T,S<:AbstractArray{T,2}},B::Base.LinAlg.LowerTriangular{T,S<:AbstractArray{T,2}}) at linalg/triangular.jl:169
+(A::Base.LinAlg.UnitUpperTriangular{T,S<:AbstractArray{T,2}},B::Base.LinAlg.UnitUpperTriangular{T,S<:AbstractArray{T,2}}) at linalg/triangular.jl:170
+(A::Base.LinAlg.UnitLowerTriangular{T,S<:AbstractArray{T,2}},B::Base.LinAlg.UnitLowerTriangular{T,S<:AbstractArray{T,2}}) at linalg/triangular.jl:171
+(A::Base.LinAlg.AbstractTriangular{T,S<:AbstractArray{T,2}},B::Base.LinAlg.AbstractTriangular{T,S<:AbstractArray{T,2}}) at linalg/triangular.jl:172
+(Da::Base.LinAlg.Diagonal{T},Db::Base.LinAlg.Diagonal{T}) at linalg/diagonal.jl:50
+(A::Base.LinAlg.Bidiagonal{T},B::Base.LinAlg.Bidiagonal{T}) at linalg/bidiag.jl:111
+{T}(B::BitArray{2},J::Base.LinAlg.UniformScaling{T}) at linalg/uniformscaling.jl:28
+(A::Base.LinAlg.Diagonal{T},B::Base.LinAlg.Bidiagonal{T}) at linalg/special.jl:103
+(A::Base.LinAlg.Bidiagonal{T},B::Base.LinAlg.Diagonal{T}) at linalg/special.jl:104
+(A::Base.LinAlg.Diagonal{T},B::Base.LinAlg.Tridiagonal{T}) at linalg/special.jl:103
+(A::Base.LinAlg.Tridiagonal{T},B::Base.LinAlg.Diagonal{T}) at linalg/special.jl:104
+(A::Base.LinAlg.Diagonal{T},B::Array{T,2}) at linalg/special.jl:103
+(A::Array{T,2},B::Base.LinAlg.Diagonal{T}) at linalg/special.jl:104
+(A::Base.LinAlg.Bidiagonal{T},B::Base.LinAlg.Tridiagonal{T}) at linalg/special.jl:103
+(A::Base.LinAlg.Tridiagonal{T},B::Base.LinAlg.Bidiagonal{T}) at linalg/special.jl:104
+(A::Base.LinAlg.Bidiagonal{T},B::Array{T,2}) at linalg/special.jl:103
+(A::Array{T,2},B::Base.LinAlg.Bidiagonal{T}) at linalg/special.jl:104
+(A::Base.LinAlg.Tridiagonal{T},B::Array{T,2}) at linalg/special.jl:103
+(A::Array{T,2},B::Base.LinAlg.Tridiagonal{T}) at linalg/special.jl:104
+(A::Base.LinAlg.SymTridiagonal{T},B::Array{T,2}) at linalg/special.jl:112
+(A::Array{T,2},B::Base.LinAlg.SymTridiagonal{T}) at linalg/special.jl:113
+(A::Base.LinAlg.Diagonal{T},B::Base.LinAlg.SymTridiagonal{T}) at linalg/special.jl:121
+(A::Base.LinAlg.SymTridiagonal{T},B::Base.LinAlg.Diagonal{T}) at linalg/special.jl:122
+(A::Base.LinAlg.Bidiagonal{T},B::Base.LinAlg.SymTridiagonal{T}) at linalg/special.jl:121
+(A::Base.LinAlg.SymTridiagonal{T},B::Base.LinAlg.Bidiagonal{T}) at linalg/special.jl:122
+{Tv1,Ti1,Tv2,Ti2}(A_1::Base.SparseMatrix.SparseMatrixCSC{Tv1,Ti1},A_2::Base.SparseMatrix.SparseMatrixCSC{Tv2,Ti2}) at sparse/sparsematrix.jl:873
+(A::Base.SparseMatrix.SparseMatrixCSC{Tv,Ti<:Integer},B::Array{T,N}) at sparse/sparsematrix.jl:885
+(A::Array{T,N},B::Base.SparseMatrix.SparseMatrixCSC{Tv,Ti<:Integer}) at sparse/sparsematrix.jl:887
+{P<:Base.Dates.Period}(Y::Union{SubArray{P<:Base.Dates.Period,N,A<:DenseArray{T,N},I<:Tuple{Vararg{Union{Colon,Range{Int64},Int64}}},LD},DenseArray{P<:Base.Dates.Period,N}},x::P<:Base.Dates.Period) at dates/periods.jl:50
+{T<:Base.Dates.TimeType}(r::Range{T<:Base.Dates.TimeType},x::Base.Dates.Period) at dates/ranges.jl:39
+{T<:Number}(x::AbstractArray{T<:Number,N}) at abstractarray.jl:442
+{S,T}(A::AbstractArray{S,N},B::Range{T}) at array.jl:782
+{S,T}(A::AbstractArray{S,N},B::AbstractArray{T,N}) at array.jl:800
+(A::AbstractArray{T,N},x::Number) at array.jl:832
+(x::Number,A::AbstractArray{T,N}) at array.jl:833
+(x::Char,y::Integer) at char.jl:40
+{N}(index1::Base.IteratorsMD.CartesianIndex{N},index2::Base.IteratorsMD.CartesianIndex{N}) at multidimensional.jl:121
+(J1::Base.LinAlg.UniformScaling{T<:Number},J2::Base.LinAlg.UniformScaling{T<:Number}) at linalg/uniformscaling.jl:27
+(J::Base.LinAlg.UniformScaling{T<:Number},B::BitArray{2}) at linalg/uniformscaling.jl:29
+(J::Base.LinAlg.UniformScaling{T<:Number},A::AbstractArray{T,2}) at linalg/uniformscaling.jl:30
+(J::Base.LinAlg.UniformScaling{T<:Number},x::Number) at linalg/uniformscaling.jl:31
+(x::Number,J::Base.LinAlg.UniformScaling{T<:Number}) at linalg/uniformscaling.jl:32
+{TA,TJ}(A::AbstractArray{TA,2},J::Base.LinAlg.UniformScaling{TJ}) at linalg/uniformscaling.jl:35
+{T}(a::Base.Pkg.Resolve.VersionWeights.HierarchicalValue{T},b::Base.Pkg.Resolve.VersionWeights.HierarchicalValue{T}) at pkg/resolve/versionweight.jl:21
+(a::Base.Pkg.Resolve.VersionWeights.VWPreBuildItem,b::Base.Pkg.Resolve.VersionWeights.VWPreBuildItem) at pkg/resolve/versionweight.jl:83
+(a::Base.Pkg.Resolve.VersionWeights.VWPreBuild,b::Base.Pkg.Resolve.VersionWeights.VWPreBuild) at pkg/resolve/versionweight.jl:129
+(a::Base.Pkg.Resolve.VersionWeights.VersionWeight,b::Base.Pkg.Resolve.VersionWeights.VersionWeight) at pkg/resolve/versionweight.jl:183
+(a::Base.Pkg.Resolve.MaxSum.FieldValues.FieldValue,b::Base.Pkg.Resolve.MaxSum.FieldValues.FieldValue) at pkg/resolve/fieldvalue.jl:43
+{P<:Base.Dates.Period}(x::P<:Base.Dates.Period,y::P<:Base.Dates.Period) at dates/periods.jl:43
+{P<:Base.Dates.Period}(x::P<:Base.Dates.Period,Y::Union{SubArray{P<:Base.Dates.Period,N,A<:DenseArray{T,N},I<:Tuple{Vararg{Union{Colon,Range{Int64},Int64}}},LD},DenseArray{P<:Base.Dates.Period,N}}) at dates/periods.jl:49
+(x::Base.Dates.Period,y::Base.Dates.Period) at dates/periods.jl:196
+(x::Base.Dates.CompoundPeriod,y::Base.Dates.Period) at dates/periods.jl:197
+(y::Base.Dates.Period,x::Base.Dates.CompoundPeriod) at dates/periods.jl:198
+(x::Base.Dates.CompoundPeriod,y::Base.Dates.CompoundPeriod) at dates/periods.jl:199
+(dt::Base.Dates.DateTime,y::Base.Dates.Year) at dates/arithmetic.jl:13
+(dt::Base.Dates.Date,y::Base.Dates.Year) at dates/arithmetic.jl:17
+(dt::Base.Dates.DateTime,z::Base.Dates.Month) at dates/arithmetic.jl:37
+(dt::Base.Dates.Date,z::Base.Dates.Month) at dates/arithmetic.jl:43
+(x::Base.Dates.Date,y::Base.Dates.Week) at dates/arithmetic.jl:60
+(x::Base.Dates.Date,y::Base.Dates.Day) at dates/arithmetic.jl:62
+(x::Base.Dates.DateTime,y::Base.Dates.Period) at dates/arithmetic.jl:64
+(a::Base.Dates.TimeType,b::Base.Dates.Period,c::Base.Dates.Period) at dates/periods.jl:210
+(a::Base.Dates.TimeType,b::Base.Dates.Period,c::Base.Dates.Period,d::Base.Dates.Period...) at dates/periods.jl:212
+(x::Base.Dates.TimeType,y::Base.Dates.CompoundPeriod) at dates/periods.jl:216
+(x::Base.Dates.CompoundPeriod,y::Base.Dates.TimeType) at dates/periods.jl:221
+(x::Base.Dates.Instant) at dates/arithmetic.jl:4
+(x::Base.Dates.TimeType) at dates/arithmetic.jl:8
+(y::Base.Dates.Period,x::Base.Dates.TimeType) at dates/arithmetic.jl:66
+{T<:Base.Dates.TimeType}(x::Base.Dates.Period,r::Range{T<:Base.Dates.TimeType}) at dates/ranges.jl:40
+(a,b,c) at operators.jl:83
+(a,b,c,xs...) at operators.jl:84
```

</div>


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

**Task 2.1***  Select 5 [random](https://www.random.org/integer-sets/) programming languages (e.g. from [GitHub highlighting repository](https://github.com/github/linguist/tree/master/vendor/grammars) or from [Wikipedia](https://en.wikipedia.org/wiki/List_of_programming_languages)). Describe each of them in terms of features you learned today and at the next lectures (~1 page; contents: language primary focus, typing system, main features, reference implementation features).
