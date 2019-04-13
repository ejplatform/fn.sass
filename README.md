# fn.sass - functional programming in Sass

[![Travis Status](https://travis-ci.org/fabiommendes/fn.sass.svg?branch=master)](https://travis-ci.org/fabiommendes/fn.sass?branch=master)
[![Appveyor Status](https://ci.appveyor.com/api/projects/status/cdy3hsbe72pmte2l?svg=true)](https://ci.appveyor.com/project/fabiommendes/fn-sass)
[![Codacy Status](https://api.codacy.com/project/badge/Grade/76887e4518e044b58dc1e1bc892111e3)](https://www.codacy.com/app/fabiommendes/fn.sass?utm_source=github.com&amp;utm_medium=referral&amp;utm_content=fabiommendes/fn.sass&amp;utm_campaign=Badge_Grade)
[![Dependencies Status](https://david-dm.org/fabiommendes/fn.sass.svg)](https://david-dm.org/fabiommendes/fn.sass)
[![Version Status](https://badge.fury.io/js/fn.sass.svg)](https://www.npmjs.com/package/fn.sass)
[![Download Status](https://img.shields.io/npm/dt/fn.sass.svg)](https://www.npmjs.com/package/fn.sass)


## What is it?

**fn.sass** is a library of functions for functional programming in Sass. Sass
is a surprisingly versatile programming language and supports many features
we normally see only in general purpose languages. Sass is Turing complete and has
rich data structures such as lists and maps, that allows it to do much more than
CSS templating.


## Should I use it?

The short answer is **no**.

Sass is more powerful than it seems and people had indeed done crazy stuff with
it <https://hugogiraudel.com/2014/01/20/json-in-sass/>`. If you really
need to do arbitrary processing in your CSS pre-processor, fn.sass is certainly
of a great help and can make many things easier.

Sass is a terrible general purpose language, and besides generating CSS, there is
nothing it can do better or more efficiently that your regular Javascript,
Python, Ruby, etc. If you can delegate work to a "real" programming language
for the most complicated bits, by all means please do so.

Don't get me wrong. Sass is excellent for its intended use of building CSS.
It is just when we stretch any language outside of its natural domain, it 
always shows its limits.


## What's up with fn.sass?

Perhaps my biggest frustration with Sass as a generic programming language is its list
implementation. We cannot mutate them, yet they do not have structural sharing
like the classic linked lists of functional languages.

This means that the obvious implementation of many useful list operations such as
mapping, filtering, etc suddenly become quadratic. To make things worst, Sass
has 3 different types of lists that can be used interchangeably most of time, but
fail in some strange corners because they are not considered to be equal to
each other (`(1 2 3) != (1, 2, 3) != [1, 2, 3]`).

**fn.sass** implements the classic "cons cell" representation of lists just like 
in LISP or any other functional language. Cons lists can be mapped, filtered, 
reduced, etc.

```scss
$xs: lst-from-array((1, 1, 2, 3, 5, 8, 13));

$ys: lst-map(sqrt, $xs);        // apply square root to all xs.
$ys: lst-filter(is-even, $xs);  // keeps only even xs.
$ys: lst-reduce(add, $xs, 0);   // reduce using add, i.e., sum all xs.
// ... many more
```

The cons cell is represented as a pair `(car cdr)`, which is just a Sass list
without commas. A list is a nested chain of cons cells ending in null, like so:
`(a (b (c null)))`. Sass renders them somewhat decently as `"a b c null"` (mind
the trailing null), so I claim is not an entirely awful encoding.

Most functions in this library are concerned with lists, but we also have a few
utilities related to Sass arrays, maps, strings and numbers. It even sports a 
very primitive testing framework that is exposed publicly and can be reused in 
your own projects.

See the [documentation](https://fabiommendes.github.io/fn.sass/) for a complete
list of functions.


### Why functional programming?

Sass occupies a strange space in language design. It uses only immutable data
structures, which hints at functional programming, but those data structures
are implemented in a very inefficient way when used with standard FP idioms.
It also claims to have first class functions, although it does not support
lambdas and closures.

The main reason for creating this library is the unacceptable O(n^2) behavior to
build large arrays and maps in a loop. This can be avoided with extremely convoluted
code, but the cleaner approach is perhaps to simply implement linked lists on top of
Sass types.

Having linked lists opens the door to many functional idioms and the next thing
you are thinking are ways to mimic function composition and partial application. Sass does not
support that, but most higher order functions in this library accept variadic
arguments that are passed to the function argument whenever it is used. It is
not like partial application, but it gets the work done:

```scss
// Standard map. apply sqrt to all xs.
$ys: lst-map(sqrt, $xs);

// Multiply all xs by 2.
$ys: lst-map(mul, $xs, 2);

// Divide 2 by each x. (we also have rsub, rmod)
$ys: lst-map(rdiv, $xs, 2);

// Convoluted equivalent of rdiv.
// Pipes can be used to mimic many forms of functional composition.
$ys: lst-map(pipe-2, $xs, flip, div);
```


### Install with npm or Yarn

```sh
$ npm install fn.sass
$ yarn add fn.sass
```

## Contributing

Want to contribute? Send a PR!


## License

Designed with ♥ and tears by Fábio Macêdo Mendes. Licensed under the [MIT License](LICENSE).
