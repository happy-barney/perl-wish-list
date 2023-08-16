
# Statement `declare`

Statement to declare new symbol in specified symbol space.

## Grammar

```
declare-statement:
    modifier "declare" SYMBOL_TABLE_IDENTIFIER SYMBOL symbol-definition? ";"

modifier:
      "my"
    | "our"
    | "local
    | "state"

SYMBOL_TABLE_IDENTIFIER:
      unqualified identifier
    | qualified identifier
    | symbol table package literal

SYMBOL:
      unqualified identifier
    | qualified identifier

symbol-definition:
      "as" "{" perl-expression "}"
    | "as" pluggable-grammar-expression

```

- `SYMBOL_SPACE_IDENTIFIER`
  Every symbol space is identified either by its short name or by its
  implementation package.

  In case of ambiguity short name variant should be preferred.

- `BAREWORD`
  newly declared symbol
  can be unqualified or fully-qualified
  can be `()` - represents unnamed symbol

- `pluggable_grammar_expression`
  expression using registered grammar
  when expression starts with brace, then regular perl grammar is used.
  Expression (both versions) is evaluated in scalar context and should return
  value to be attached with symbol

Note:
Pattern `pluggable grammar expression` or `{ perl expression }` should be
used consistently in all extensions involving pluggable grammar expressions.

## [Open question](https://github.com/happy-barney/perl-wish-list/discussions/3)

- how to store symbol value?
- how to store independent symbols in pad?

## Symbol value

we need to store
- referencing (thus SV comes handy)
- symbol table identification (+ `int`)
- symbol table specific data (+ `void *`)

Sounds to me like new `?V` type (`YV`?)

To store symbol values `GV` should be extended with
- `YV **   array`
- `SSize_t array_allocated_size`

(not memory optimal, but first make it work, optimize later)

## Warnings / errors

- new error _symbol space does not exist_

- new error _symbol space doesn't allow declare qualified identifiers_

- new error _symbol space doesn't allow my/local/our/state symbols_

- warning `redefine` - when symbol is already defined
  - for every symbol map new dynamic warning category `redefine::<identifier>`
    should be also available

## Symbol package

Will provide symbol space specification and / or hooks.

Described [symbol-space-package.md](symbol-space-package.md)

## Examples

- declare new symbol space
```
package Foo {
    declare symbol_table foo as __PACKAGE__;
}
```

```
package Foo {
    declare symbol_table foo as __PACKAGE__,
        allow_qualified_symbols => true,
        ...
        ;
}
```

- declare attributes
```
package Catalyst {
    declare attribute GET as on CODE do { ... };
}

use Catalyst :attribute(GET);

sub route :GET { ... }

declare attribute exporable as
    on CODE    do { push @EXPORT_OK, $name }
    on SCALAR | ARRAY | HASH do { push @EXPORT_OK, $sigil . $name }
    ;
```

- declare attrbites with discriminator
```
declare attribute exportable [ CODE   ] as
    do { push @EXPORT_OK, $name }
    ;

declare attribute exportable [ SCALAR | ARRAY | HASH ] as
    do { push @EXPORT_OK, $sigil . $name }
    ;
```

- declare new data contacts
```
declare contract Even as Int where { ! $_ % 2 };
declare contract UUID as Str format (qr/.../);
declare contract Array_Of_Foo as Array[Foo] min_length (FOO) max_length (BAR);
```

- declare new constant (replacing `use constants`)
```
declare constant FOO as 1_024 :contract(Int);
```

