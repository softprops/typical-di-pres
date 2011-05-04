!SLIDE

# Let's talk *cake*

!SLIDE

# 3 ingredients

* 1 part modules
* 1 part dependencies
* 1 part namespace

!SLIDE
# 1. The module

aka the *component*

declares the *what* (dependencies)
<div class="pre"><code>
trait RobotModule {
  type Operation = (Int, Int) => Int
  <em>def operation: Operation</em>
  def perform(x: Int, y: Int) = operation(x, y)
}
</code></div>

!SLIDE

# 2. The dependencies

implements the *how*
<div class="pre"><code>
trait MultiplyOperation
  extends ((Int, Int) => Int) {
  def apply(x: Int, y: Int) = <em>x * y</em>
}
</code></div>

!SLIDE

# 3. The namespace

aka the *registry*, *world*, *env*

binds modules and dependencies
<div class="pre"><code>
object DefaultRobot extends <em>RobotModule</em> {
   <span class="com">// all configuration happens here</span>
   def <em>operation</em>: ((Int, Int) => Int) =
     new MultiplyOperation {}
}
</code></div>
!SLIDE

    import DefaultRobot._

<div class="pre"><code>
perform(2, 4) <span class="com">// 8</span>
perform(8, 2) <span class="com">// 16</span>
</code></div>

!SLIDE
<div class="pre"><code>trait <em>TestRobot</em> extends <em>RobotModule</em> {
  def operation = (x: Int, y: Int) => x + y
}
</code></div>

<div class="pre"><code>
object RobotSpec extends <em>TestRobot</em>
  with Specification {
  "robot" should {
     "perform" in { <em>perform</em>(4, 2) must be(6) }
  }
}
</code></div>

!SLIDE

# why is this cool?

!SLIDE

# <em>self</em>ish stackability
<div class="pre"><code>trait <em>IqModule</em> { def iq: Int }
trait Job {
  def suitable(iq: Boolean)
  def perform(op: (Int => Int) => Int)
}
trait RobotModule {
  <em>self: IqModule =></em><span class="com">// mv concern to a new module</span>
  def perform(j: Job) =
    if(j.suitable(iq)) j.perform(operation)
    else error("inferior iq error")
}
</code></div>
