
# Motivation

As many new versions of Perl comes and lot of projects are stucked
with older version, writing CPAN modules as well as nice code for
older versions may become nightmare.

## Related topics

- Perl 7 (kind of)
- CPAN multi index

## Proposal

Change behaviour of `use VERSION` from _minimal required_ to
_behave like this version_.

See [full spec](spec/spec.md) for details.

# Examples (pseudocode to show intention)

```
use v5.12;

package Foo { ... };
```

Using syntax construct from higher version than declared will cause syntax error
even if executed on perl supporting given syntax

# Support by other languages

## Typescript

As far as typescript is transliterated language it can use newer syntax
and yet to be transliterated into older versions of javascript.

