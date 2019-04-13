# Underscore for Sass (?)

[![Travis Status](https://travis-ci.org/fabiommendes/fn.sass.svg?branch=master)](https://travis-ci.org/fabiommendes/fn.sass?branch=master)
[![AppVeyor Status](https://ci.appveyor.com/api/projects/status/xcm8meymwerq0r82?svg=true)](https://ci.appveyor.com/project/cjpatoilo/fn.sass)
[![Codacy Status](https://img.shields.io/codacy/grade/848fb4bd6902434fab0bcfb5461284fe/master.svg)](https://www.codacy.com/app/fabiommendes/fn.sass/dashboard)
[![Dependencies Status](https://david-dm.org/fabiommendes/fn.sass.svg)](https://david-dm.org/fabiommendes/fn.sass)
[![Version Status](https://badge.fury.io/js/fn.sass.svg)](https://www.npmjs.com/package/fn.sass)
[![Download Status](https://img.shields.io/npm/dt/fn.sass.svg)](https://www.npmjs.com/package/fn.sass)
[![Gitter Chat](https://img.shields.io/badge/gitter-join_the_chat-4cc61e.svg)](https://gitter.im/fabiommendes/fn.sass)


## What is it?

**fn.sass** is a library of functions for functional programming in Sass. Sass
is a surprisingly versatile programming language and supports many features
we normally see only in general purpose languages. Sass is Turing complete and has
rich data structures such as lists and maps, that allows it to do much more than
CSS templating.


## Should I use it?

The short answer is **no**. Sass is powerful and people had done crazy stuff
with it <https://hugogiraudel.com/2014/01/20/json-in-sass/>`. If you really
have to do arbitrary processing in your CSS pre-processor, fn.sass is certainly
of a great help and can make many things easier. If you can avoid it, please
do so! Sass is a terrible language, and besides generating CSS, there is
nothing it can do better or more efficiently that your regular Javascript,
Python, Ruby, etc. If you can delegate work to a "real" programming language
for the most complicated bits, by all means please do so.

Don't get me wrong: Sass is excellent for its intented purpose of building CSS.
It is just when we stretch the language outside those boundaries it starts showing
its limits.


## What's up with fn.py?

Perhaps my biggest grip with Sass is its list implementation. We cannot mutate
them, yet they do not have structural sharing like the classic linked lists of
functional languanges.

This means that the naive implementation of many useful list operations such as
mapping, filtering, etc suddenly become quadratic. To make things worst, Sass
has 3 different types of lists that can be used interchangeably most of time
and fail in some strange corners because they are not considered equal to each other.

fn.sass implements the classic "cons cell" representation of lists just like in
LISP or any functional languange. This cons list can be mapped, filtered, reduced,
etc.

```scss
$xs: lst-from-array((1, 1, 2, 3, 5, 8, 13));

lst-map(op-add, $xs, 1);    // add 1 to each x in list of xs.
lst-filter(is-even, $xs);   // assuming is-even exists, keeps only even xs.
lst-reduce(op-sum, $xs, 0); // reduce using sum, i.e., sum all  xs.
// ... many more
```

The cons cell is represented as a pair `(car cdr)` and a list as a nested
chain of cons cells ending in null, `(a (b (c null)))`. This is not super-efficient,
but at least it allows doing the operations above in O(n) rather than O(n^2)
with standard Sass arrays.

Most functions in this library are concerned with lists, but we also have a few
utilities related to standard Sass immutable arrays, maps, strings and
numbers. It even sports a very primitive testing framework that is exposed
publicly and can be reused in your own projects.


### Why functional programming?

Sass ocupies a strange space in language design. It uses only immutable data
structures, which hints at functional programming, but those data structures
are implemented in a very unefficient way. It also claims to have first class
functions, although it does not support lambdas and closures.

The main reason for me writing this library is the unacceptable O(n^2) behavior to
build arrays and maps on a loop. This can be avoided with extremely convoluted code, but
the cleaner approach is to simply implement linked lists on top of Sass types.

Linked lists lead to many functional idioms and the next thing you are thinking
in ways to mimmick function composition and curying. Most higher order functions
in this library accept variadic arguments that are passed to the function
argument whenever it is used. It is almost like currying, but the caller that
performs the partial application.

We saw this in action in the example above, but perhaps it was not very clear.
Let us recapitulate:

```scss
// standard map. apply sqrt to each x.
lst-map(sqrt, $xs);

// "partially apply" 2 on rhs of op-mul
lst-map(op-mul, $xs, 2);

// convoluted equivalent of using op-rdiv in the operation above
lst-map(lst-pipe-2, $xs, op.flip, op.div);
```


### Install with npm

```sh
$ npm install fn.sass
```

### Install with Yarn**

```sh
$ yarn add fn.sass
```


## Contributing

Want to contribute? Follow these [recommendations](https://github.com/fabiommendes/fn.sass/blob/master/.github/contributing.md).


## License

Designed with ♥, blood and tears by Fábio Macêdo Mendes. Licensed under the [MIT License](LICENSE).
