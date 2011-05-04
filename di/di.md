
!SLIDE

# the *how*
aka *tight*<sup>[1]</sup> coupling
<br/><br/>
<div class="pre"><code>class Robot {
  def perform(x: Int, y: Int) = <em>x * y</em>
}
</code></div>
<div class="pre"><code>
new Robot().perform(2, 10) <span class="com">// 20</span>
</code></div>
<sub>1. tight is bad</sub>

!SLIDE

# the *what*

aka *loose*<sup>[1]</sup> coupling

<div class="pre"><code>
class Robot(<em>op: ((Int, Int) => Int)</em>) {
  def perform(x: Int, y: Int) = <em>op(x, y)</em>
}
</code></div>
<div class="pre"><code>
val multiply = <em>(x: Int, y: Int) => x * y</em>
new Robot(<em>multiply</em>)
  .perform(2, 10) <span class="com">// 20</span>
</code></div>
<sub>1. loose is good</sub>

!SLIDE

# the *why*
<div class="pre"><code>
"dependency" should {
   "be testable" in {
      val <em>multiply</em> = <em>(x: Int, y: Int) => x * y</em>
      <em>multiply(</em>2, 10<em>)</em> must be(20)
      new Robot(<em>multiply</em>)
        .perform(2, 10) must be(20)
   }
}
</code></div>

!SLIDE

# How does *Scala* fit in?

!SLIDE

# Perfectly!

* Spring
* Guice
* you-name-it Java DI library
* *Cake*
* *Typeclasses*? maybe<sup>[1]</sup>...

<sub>1 toying with the idea</sub>
