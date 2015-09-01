---
layout: default
title:  "Semigroup"
section: "typeclasses"
source: "https://github.com/non/algebra/blob/master/core/src/main/scala/algebra/Semigroup.scala"

---
# Semigroup

A semigroup for some given type A has a single operation
(which we will call `combine`), which takes two values of type A, and
returns a value of type A. This operation must be guaranteed to be
associative. That is to say that:

    ((a combine b) combine c)

must be the same as
     
    (a combine (b combine c))

for all possible values of a,b,c. 

There are instances of `Semigroup` defined for many types found in the
scala common library:

```scala
scala> import cats._
import cats._

scala> import cats.std.all._
import cats.std.all._

scala> Semigroup[Int].combine(1, 2)
res0: Int = 3

scala> Semigroup[List[Int]].combine(List(1,2,3), List(4,5,6))
res1: List[Int] = List(1, 2, 3, 4, 5, 6)

scala> Semigroup[Option[Int]].combine(Option(1), Option(2))
res2: Option[Int] = Some(3)

scala> Semigroup[Option[Int]].combine(Option(1), None)
res3: Option[Int] = Some(1)

scala> Semigroup[Int => Int].combine({(x: Int) => x + 1},{(x: Int) => x * 10}).apply(6)
res4: Int = 67
```

Many of these types have methods defined directly on them,
which allow for such combining, e.g. `++` on List, but the
value of having a `Semigroup` typeclass available is that these
compose, so for instance, we can say 
 
```scala
scala> import cats.implicits._
import cats.implicits._

scala> Map("foo" -> Map("bar" -> 5)).combine(Map("foo" -> Map("bar" -> 6), "baz" -> Map()))
res5: Map[String,scala.collection.immutable.Map[String,Int]] = Map(baz -> Map(), foo -> Map(bar -> 11))

scala> Map("foo" -> List(1, 2)).combine(Map("foo" -> List(3,4), "bar" -> List(42)))
res6: Map[String,List[Int]] = Map(foo -> List(1, 2, 3, 4), bar -> List(42))
```

which is far more likely to be useful than

```scala
scala> Map("foo" -> Map("bar" -> 5)) ++  Map("foo" -> Map("bar" -> 6), "baz" -> Map())
res7: scala.collection.immutable.Map[String,scala.collection.immutable.Map[_ <: String, Int]] = Map(foo -> Map(bar -> 6), baz -> Map())

scala> Map("foo" -> List(1, 2)) ++ Map("foo" -> List(3,4), "bar" -> List(42))
res8: scala.collection.immutable.Map[String,List[Int]] = Map(foo -> List(3, 4), bar -> List(42))
```


There is inline syntax available for `Semigroup`. Here we are 
following the convention from scalaz, that`|+|` is the 
operator from `Semigroup`.

```scala
scala> import cats.syntax.all._
import cats.syntax.all._

scala> import cats.implicits._
import cats.implicits._

scala> import cats.std._
import cats.std._

scala> val one = Option(1)
one: Option[Int] = Some(1)

scala> val two = Option(2)
two: Option[Int] = Some(2)

scala> val n: Option[Int] = None
n: Option[Int] = None

scala> one |+| two
res9: Option[Int] = Some(3)

scala> n |+| two
res10: Option[Int] = Some(2)

scala> n |+| n
res11: Option[Int] = None

scala> two |+| n
res12: Option[Int] = Some(2)
```

You'll notice that instead of declaring `one` as `Some(1)`, I chose
`Option(1)`, and I added an explicit type declaration for `n`. This is
because there aren't typeclass instances for Some or None, but for
Option. If we try to use Some and None, we'll get errors:

```scala
scala> Some(1) |+| None
<console>:30: error: value |+| is not a member of Some[Int]
       Some(1) |+| None
               ^

scala> None |+| Some(1)
<console>:30: error: value |+| is not a member of object None
       None |+| Some(1)
            ^
```

N.B.
Cats does not define a `Semigroup` typeclass itself, it uses the [`Semigroup`
trait](https://github.com/non/algebra/blob/master/core/src/main/scala/algebra/Semigroup.scala)
which is defined in the [algebra project](https://github.com/non/algebra) on 
which it depends. The [`cats` package object](https://github.com/non/cats/blob/master/core/src/main/scala/cats/package.scala)
defines type aliases to the `Semigroup` from algebra, so that you can
`import cats.Semigroup`.
