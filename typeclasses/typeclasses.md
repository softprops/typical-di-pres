!SLIDE

# let's talk typeclasses

!SLIDE

# <em>type</em>classes?

!SLIDE

# Put on your 3-d
# <span class="threed">type-glasses</span>,
## here comes some <span class="threed">haskell</span>!

!SLIDE

<div class="pre hs"><code>class Falsy t where
  falsy :: t -> Bool
instance Falsy Bool where
  falsy False = True
  falsy _ = False
instance Falsy Int where
  falsy 0 = True
  falsy _ = False
instance Falsy [a] where
  falsy [] = True
  falsy _ = False
instance Falsy (Maybe a) where
  falsy Nothing = True
  falsy _ = False
</code></div>

!SLIDE

How Haskell...

declares a <em>typeclass</em>

<div class="pre"><code>
class <em class="emtc">Concept</em> <em class="emt">t</em> where
  <em class="emsig">methodA :: signatureA</em>
  <em class="emsig">methodB :: signatureB</em>
</code></div>

<div class="pre"><code>
<em class="emtc">Typeclass</em>
<em class="emt">Generic Type</em>
<em class="emsig">Typeclass method signature</em>
</code></div>

!SLIDE

How Haskell...

declares an '<em>instance</em>' of a typeclass

<div class="pre"><code>
instance <em class="emtc">Concept</em> <em class="emt">Foo</em> where
  <em class="emsig">methodAForTypeFoo</em>
  <em class="emsig">methodBForTypeFoo</em>
</code></div>

<div class="pre"><code>
<em class="emtc">Typeclass</em>
<em class="emt">Specific Type</em>
<em class="emsig">Typeclass method signature</em>
</code></div>

!SLIDE

# Only one instance* of a <em>typeclass</em> for every supported type
\*haskell requirement to avoid ambiguity

!SLIDE

# <em>Typeclasses</em> provide support for <em>adhoc polymorphic</em> interfaces
polymorphism w/o subtyping. <em>Comparable</em> vs <em>Comparator</em>

!SLIDE

# what does all this have to do with <em>Scala</em> & <em>DI</em>?

!SLIDE

# a very convenient implementaion
# pattern :)

!SLIDE

# a typeclass

<div class="pre"><code>
trait <em>TypeClass</em>[GenericType] {
  def method(param: GenericType): SomeOtherType
}
</code></div>
yep. nothing special here.

!SLIDE

# typeclass instances
<div class="pre"><code>object TypeClass {
  <em>implicit</em> object TypeClassFoo
    extends <em>TypeClass[Foo]</em> {
     def method(a: Foo) = // ...
  }
  <em>implicit</em> object TypeClassBar
    extends <em>TypeClass[Bar]</em> {
     def method(a: Bar) = // ...
  }
}
</code></div>

!SLIDE

# resolving instances

<div class="pre"><code><span class="com">// 2.7 implicit param</span>
def typeclassed[T](x: T)(<em>implicit tc: TypeClass[T]</em>) =
  <em>tc.method</em>(x)
</code></div>

<div class="pre"><code>
<span class="com">// 2.8 context bound</span>
def typedclassed[T: TypeClass](x: T) =
  <em>implicitly</em>[<em>TypeClass[T]</em>].method(x)
</code></div>

<div class="pre"><code>
<span class="com">// 2.8 implicit resolver</span>
<em>implicitly</em>[<em>TypeClass[Bar]</em>].method(new Bar)
</code></div>

!SLIDE

case study
<div class="pre"><code>
<span class="com">//kinda like dynamic languages' `falsy` values</span>
trait <em>Falsy</em>[T[_]] {
  def apply[A](x: T[A]): Boolean
}
</code></div>

!SLIDE
<div class="pre"><code>
<span class="com">// falsy typeclass instances</span>
object Falsy {
  <em>implicit</em> object <em>FalsyList</em>
     extends Falsy[List] {
     def apply[T](x: List[T]) = x match {
       case Nil => true
       case _ => false
     }
   }
   <span class="com">// ... </span>
</code></div>

!SLIDE
<div class="pre"><code><span class="com">// con't</span>
   <em>implicit</em> object <em>FalsyOption</em> extends Falsy[Option] {
     def apply[T](x: Option[T]) = x match {
       case None => true
       case _ => false
     }
  }
}
</code></div>

!SLIDE
<div class="pre"><code>
object Iffy {
   import Falsy._
   <span class="com">// falsy control struct</span>
   def fif[A[_], B, C](cond: => A[B])(
     pass: => C)(
     fail: => C)(
     implicit f: Falsy[A]): C = {
       if(!<em>f</em>(cond)) pass else fail
   }
}
</code></div>
!SLIDE
     import Iffy._

<div class="pre"><code>
fif(List.empty[Int]) {
  println("fail!")
} {
  println("falsy!")
}
  <br/>
fif(Some(1)) {
  println("not falsy!")
} {
  println("fail!")
}
</code></div>
