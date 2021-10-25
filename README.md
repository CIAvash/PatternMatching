NAME
====

PatternMatching

DESCRIPTION
===========

PatternMatching is a [Raku](https://www.raku-lang.ir/en/) module for pattern matching.

It provides `match_pattern` infix operator and `┇` as an alias for it. It accepts a topic and a list of patterns(`Code`s) and can be used like `$topic match_pattern -> $some_pattern {...}`.

If the topic doesn't match any of the patterns, a `Failure` containing the `X::PatternMatching::MatchFailed` exception will be returned.

Info for `┇`:

  * Code: U+2507

  * Name: BOX DRAWINGS HEAVY TRIPLE DASH VERTICAL

SYNOPSIS
========

```raku
use PatternMatching:auth<zef:CIAvash>;

class C { has $.a = 'attribute' }

my @a = 1, [2, 3], 'a', <b c>, :d(4), :e[5, 6], 10, 'f', 'g', 'h', 20,
        C.new, PatternMatching, :g(8, 2), [7, 8, 9];

my @b = @a.map: * match_pattern
          -> 10 { 'ten' },
          -> Int $a where * > 10 { 'twenty' },
          -> Int $a { $a + 2 },
          -> Str $a where any <f g h> { 'f or g or h' },
          -> Str $a { $a.succ },
          -> C (:$a) { $a },
          -> @ (Str $a, Str $b) { join ' | ', $a, $b },
          -> @ (Int $a, Int $b) { $a + $b },
          -> @ (Int $a, *@b where .all ~~ Int) { [+] $a, |@b },
          -> Pair (:key($a), :value([$b, $c])) { "$a: {$b + $c}" },
          -> Hash() (:$g ($a, $b)) { "$g: {$a + $b}" },
          -> Pair $pair { $pair.kv.join: ': ' },
          -> | { 'default' }

put @b.raku;
=output [3, 5, "b", "b | c", "d: 4", "e: 11", "ten", "f or g or h", "f or g or h", "f or g or h", "twenty",
"attribute", "default", "g: 10", 24]␤

my @c = @a.map: * ┇
          -> @ (Str $a, Str $b) { join ' | ', $a, $b },
          -> @ (Int $a, Int $b) { $a + $b },
          -> | { 'default' }

put @c.raku;
=output ["default", 5, "default", "b | c", "default", "default", "default", "default", "default", "default",
"default", "default", "default", "default", "default"]␤

for @a {
    .put with .&[match_pattern]:
                -> 10 { 'ten' },
                -> Int $a where * > 10 { 'twenty' },
                -> Int $a { $a + 2 },
                -> Str $a where any <f g h> { 'f or g or h' },
                -> Str $a { $a.succ },
                -> C (:$a) { $a };
                -> @ (Str $a, Str $b) { join ' | ', $a, $b },
                -> @ (Int $a, Int $b) { $a + $b },
                -> @ (Int $a, *@b where .all ~~ Int) { [+] $a, |@b },
                -> Pair (:key($a), :value([$b, $c])) { "$a: {$b + $c}" },
                -> Hash() (:$g ($a, $b)) { "$g: {$a + $b}" },
                -> Pair $pair { $pair.kv.join: ': ' },
}
```

INSTALLATION
============

You need to have [Raku](https://www.raku-lang.ir/en) and [zef](https://github.com/ugexe/zef), then run:

```console
zef install --/test "PatternMatching:auth<zef:CIAvash>"
```

or if you have cloned the repo:

```console
zef install .
```

TESTING
=======

```console
prove -ve 'raku -I.' --ext rakutest
```

SUBS
====

## sub infix:<match_pattern>

```raku
sub infix:<match_pattern>(
    $topic,
    *@functions where { ... }
) returns Mu
```

Matches `topic` against a list of patterns(functions).

Returns `Failure`(`X::PatternMatching::MatchFailed`) when no pattern is matched.

## sub infix:<┇>

```raku
&infix:<┇> = &[match_pattern]
```

Alias for `match_pattern`

ERRORS
======

If the topic doesn't match any of the patterns, a `Failure` containing the `X::PatternMatching::MatchFailed` exception will be returned.

REPOSITORY
==========

[https://github.com/CIAvash/PatternMatching/](https://github.com/CIAvash/PatternMatching/)

BUG
===

[https://github.com/CIAvash/PatternMatching/issues](https://github.com/CIAvash/PatternMatching/issues)

AUTHOR
======

Siavash Askari Nasr - [https://www.ciavash.name](https://www.ciavash.name)

COPYRIGHT
=========

Copyright © 2021 Siavash Askari Nasr

LICENSE
=======

This file is part of PatternMatching.

PatternMatching is free software: you can redistribute it and/or modify it under the terms of the GNU Lesser General Public License as published by the Free Software Foundation, either version 3 of the License, or (at yoption) any later version.

PatternMatching is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU Lesser General Public License for more details.

You should have received a copy of the GNU Lesser General Public License along with PatternMatching. If not, see <http://www.gnu.org/licenses/>.
