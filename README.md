# prex

Approximate Regular Expression Matching for Java. prex determines
the matching distance between a string *s* and a regular expression *r*,
*i.e.* how many insertions, deletions and substitutions on *s* are at least required (minimum cost) such that the resulting
string *s'* is acceptable by *r*.

# Status


[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?style=flat-square)][licence]
[![Language](http://img.shields.io/badge/language-java-brightgreen.svg?style=flat-square)][language]
[![Linux Build Status](https://img.shields.io/travis/julianthome/prex/master.svg?label=Linux%20build)][travis]
[![Test Coverage](https://codecov.io/gh/julianthome/prex/branch/master/graph/badge.svg)][coverage]
[![Code Climate](https://codeclimate.com/github/julianthome/prex/badges/issue_count.svg)][codeclimate]

[licence]: https://opensource.org/licenses/mit
[language]: https://www.java.com
[travis]: https://travis-ci.org/julianthome/prex
[codeclimate]: https://codeclimate.com/github/julianthome/prex
[coverage]: https://codecov.io/gh/julianthome/prex


# Approach

The prex algorithm is inspired by the [work of Myers and
Millers](http://www.cs.mun.ca/~harold/Courses/Old/Ling6800.W06/Diary/reg.aprox.pdf)
who provide an algorithm for solving the approximate regular
expression matching problem. However, their approach requires a special pre-computed
data-structure called edit-graph *e* that encodes all possible matches and mismatches
of *s* with respect to
*r* which requires approximately 15xSxR edges and SxR vertices
where S denotes the length of *s* and R is the length of *r*.
Given that data structure, their approach searches a minimal cost path
in *e* with a worst-case time complexity of
O(SxR). The main strengths
of Myers and Millers approach are the possibility to use real-valued costs for insertion,
deletion and substitution, respectively, and its worst-case time complexity. The drawback, however,
is that the edit-graph has to be created upfront.

Similar to Myers and Millers approach, one can also define real-valued cost
for deletion, insertion and substitution in prex. However, prex is based on the branch and bounds method
(+ extended list) and does not rely on a precomputed edit-graph. Because,
prex is using branch and bounds, in theory it has the same worst-case time complexity as exhaustive
search. However, our goal is to keep
the algorithm as simple as possible and to achieve a reasonable performance in practice by incorporating heuristics.

# Usage

prex can be used as a standalone command line tool or as a library.

## Standalone

One could compile prex by running `mvn package` on the command line. The resulting `jar` archive
can be called with `java -jar prex.jar` and provides the following command line options:

| Option               | Description                                         |
|----------------------|:----------------------------------------------------|
| -c,--cost &lt;cost&gt;   |cost values &lt;insertion&gt; &lt;substitution&gt; &lt;deletion&gt;|                      
| -h                   |  print this message                                 |
| -ic,--ignore-case    |  ignore case of s when matching against r           |
| -n,--normalize       |      normalize cost to a value in range [0,1]       |
| -r,--regex &lt;regex&gt; |  regular expression                                 |
| -s,--string &lt;string&gt; |  constant string                                  |
| -t,--print-tree      |  print branch and bounds edit tree                  |

Please note that the regex syntax is based on [dk.brics](https://www.brics.dk/automaton/doc/index.html?dk/brics/automaton/RegExp.html) and that paramters passed to prex should be quoted to prevent globbing. In case you need quotes with inside your string or you regex pattern, you could escape them as illustrated in the example below.

```
java -jar prex.jar -c 1.0 1.0 1.0 -s "\"" -r "\\\""
input-string: '"'
input-regex: '\"'
Cost : 0.0
```

The pattern `\"` for the `s` parameter is equivalent to the quote character since it is treated as a normal Java string. The parameter passed through the `r` paramter is interpreted differently, because the regular expression syntax is based on [dk.brics](https://www.brics.dk/automaton/doc/index.html?dk/brics/automaton/RegExp.html); the pattern `\\\"` is interpreted as a quote charater.

## Library

The following code snippet shows how to use prex after the `jar` file
is imported into your project:

```java
// invoke Prex constructor with regular expression definition,
// r = gcg[abc]+
// default costs for substitution, insertion and deletion are 1.0,
// but they are modifiable
Prex am = new Prex("gcg[abc]+");
// get cost to make s = GCGa matching r = gcg[abc]+
// ignore case and return a value in range [0,1]
System.out.println(am.evaluateCost("GCGa", true, true));
// get cost to make s = GCGa matching r = gcg[abc]+
// do not ignore case and return a value in range [0,1]
System.out.println("GCGa", false, true));
// ignore case and do not normalize
System.out.println(am.evaluateCost("GCGa", true, false));
// do not ignore case and do not normalize
System.out.println(am.evaluateCost("GCGa", false, false));
```

The results of the invocations above look as follows:

```bash
0.0
0.75
0.0
3.0
```

Please have a look at the test cases and constructors provided by the
`Prex` Java class to get additional information about the usage of prex.

# Licence

The MIT License (MIT)

Copyright (c) 2016 Julian Thome <julian.thome.de@gmail.com>

Permission is hereby granted, free of charge, to any person obtaining a copy of
this software and associated documentation files (the "Software"), to deal in
the Software without restriction, including without limitation the rights to
use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies
of the Software, and to permit persons to whom the Software is furnished to do
so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
