# Stream

This document defines the `Data.Stream` package. A *stream* has the following characteristics:

* Superficially it looks like and behaves like a list, and
* It is infinitely long and therefore some standard list operations makes no sense - `length` and `foldLeft` to mention 2.


## Function Definitions

This section lists the functions contained within the `Data.Stream` package.

To assist with the definitions and the tests the following streams of natural and Fibonacci numbers respectively are defined.  Note that the stream of Fibonacci numbers defined is the sequence that starts with $$0$$ rather than $$1$$.

~~~ +TestHelpers
naturalNumbersFrom : Int -> Stream Int
naturalNumbersFrom n =
  Stream n (\() -> naturalNumbersFrom (n + 1))

naturalNumbers =
  naturalNumbersFrom 0
~~~


~~~ +TestHelpers
nextFibonacci : Int -> Int -> Stream Int
nextFibonacci a b =
  Stream a (\() -> nextFibonacci b (a + b))

fibonacci =
  nextFibonacci 0 1
~~~


### head

Returns the first element in a stream.

~~~ +SourceExports, weave = False
head
~~~

~~~ +SourceFunctions
head : Stream a -> a
head (Stream x _) = x
~~~

The definition of `head` is trivial and the tests to confirm accuracy are equally trivial.

~~~ +Tests, weave = False
Describe "head" [
  [=HeadTests, separator = "\n"]
]
~~~

~~~ +HeadTests
Test "naturalNumbers" <| head naturalNumbers == 0
~~~

~~~ +HeadTests
Test "naturalNumbersFrom 10 " <| head (naturalNumbersFrom 10) == 10
~~~

~~~ +HeadTests
Test "fibonacci" <| head fibonacci == 0
~~~


### tail

Returns a stream after dropping off the first element.

~~~ +SourceExports, weave = False
tail
~~~

~~~ +SourceFunctions
tail : Stream a -> Stream a
tail (Stream _ xs) = xs
~~~

The definition of `tail` is trivial and, like `head`, the tests to confirm accuracy are equally trivial.

~~~ +Tests, weave = False
Describe "tail" [
  [=TailTests, separator = "\n"]
]
~~~

~~~ +TailTests
Test "naturalNumbers" <| (head <| tail naturalNumbers) == 1
~~~

~~~ +TailTests
Test "naturalNumbersFrom 10 " <| (head <| tail <| naturalNumbersFrom 10) == 11
~~~

~~~ +TailTests
Test "fibonacci" <| (head <| tail <| tail <| tail fibonacci) == 2
~~~


### take

Returns a stream after dropping off the first element.

~~~ +SourceExports, weave = False
take
~~~

~~~ +SourceFunctions
take : Int -> Stream a -> List a
take n stream =
  if n < 1 then
    []
  else
    (head stream) :: (take (n - 1) (tail stream))
~~~

The definition of `take` is recursive returning a list of elements.

~~~ +Tests, weave = False
Describe "take" [
  [=TakeTests, separator = "\n"]
]
~~~

~~~ +TakeTests
Test "6 naturalNumbers" <| take 6 naturalNumbers == [0, 1, 2, 3, 4, 5]
~~~

~~~ +TakeTests
Test "6 fibonacci" <| take 6 fibonacci == [0, 1, 1, 2, 3, 5]
~~~


## Boilerplate

In order to kick off the implementation there is a need for some boilerplate code.  The first is the `Stream` source code:

~~~ Source, file = "Stream.sle"
export
  [=SourceExports, separator = "\n  , "]

[=SourceFunctions, separator = "\n\n"]
~~~

The second piece of boilerplate code is to hold all of the unit tests that are composed as part of this definition:

~~~ Tests, file = "StreamTest.sle"
export
  suite


import file:../../Test/Unit exposing Suite(..)
import file:./Stream

[=TestHelpers, separator = "\n\n"]

suite =
  Describe "Tools.Lsls.Stream"
  [ [=Tests, separator = "\n    , "]
  ]
~~~
