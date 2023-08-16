
# Follow-ups and possible extensions

Assorted ideas how independent symbol spaces behaviour may be used
or extended.

## custom identifier grammar

For domains where identifier may have different grammar, for example
web service framework

```
declare route GET /api/vi/foo/:foo_name/bar as sub {
    if ($foo_name =~ ...) ....
}
```

## capability to declare symbol space sigil

Feature may be useful for various DSL

```
declare sigil $ as
    := symbol_space => CORE::scalar
    := behaviour    => SCALAR REFERENCE
    ;

declare sigil @ as
    := symbol_space => CORE::array
    := behaviour    => LIST ARRAY
    ;

declare sigil % as
    := symbol_space => CORE::array
    := behaviour    => LIST HASH
    ;

declare sigil ¤ as
    := symbol_space => My::DSL::Variable
    := behaviour    => SCALAR
    ;
```

## internal symbol space - regexp

Declared named regexps to be usable in regexp as named pattern references.

Example: (borrowing `PPR` example):
```
# in PPR module
declare regexp PerlBlock as ...;

# in My::Module

use PPR
    := regexp => PerlBlock
    ;

if ($value =~ qr ((?<Block>(?&PerlBlock)))) ...
```

## internal symbol space - constant

Replacement for `use constant` with significant change - `local` is not allowed.

Symbol space `constant` also extends symbol space `builtin::sub`.

```
declare constant FOO as 1;
declare constant BAR as 1..10;
```

## internal symbol space - using

Attempt to heal smartmatch by defining rules based on data contract.
Example using issue discussed at p5p - string compare or regex

```
define using Regexp_Or_Eq as
  on Regexp { $a =~ $b }
  on Str    { $a eq $b }
  ;

given ($value using Regexp_Or_Eq) {
    when (...) { ... }
}

if (:= using => Regexp_Or_Eq $value ~~ $something) ...
```

## allow out-of-band data stored with symbol

Example:
```
declare contract Foo ...;
${ declared contract Foo } = ...;
@{ declared contract Foo } = ...;
%{ declared contract Foo } = ...;
```

- check existence / delete attached value
```
if (exists ${ declared ... }) { ... }
delete ${ declared ... };
```

## new Grammar module

Variant of Raku's `Grammar`, but with better separation of concerns.
Rules and tokens are not methods
(possible conflict with module methods, eg `CREATE` or `before` in Raku's case)

Using snippet from `perly.y` as an example

```
declare rule else as
    | KW_ELSE mblock
    | KW_ELSIF condition_clause mblock else
    ;

declare rule condition_clause as
    PERLY_PAREN_OPEN mexpr PERLY_PAREN_CLOSE
    ;
```

Spark idea: Should this module be able to generate tables for Perl's
parser it may be used incorporate custom grammars into current parser
at runtime.
