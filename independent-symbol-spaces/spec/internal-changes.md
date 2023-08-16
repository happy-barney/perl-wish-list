
# Internal changes

## symbol space accessor

`int` value representing registered symbol spaces (~ stashes).

Internal symbol spaces will have constant indexes.
Independent symbol spaces will get next value in sequence.

## GV

Currently declared symbols are stored in `GV`, providing lookup in form
```
$all_symbols {symbol name} [symbol space]
```

With growing usage of independent symbol spaces as well as choosing
naming best practices `GV` may become too expensive (sparse map),
in worst case scenario it may hold only one not-NULL value per instance.

So we should also consider refactoring to following lookup in near future
```
$all_symbols [symbol space] {symbol name}
```

Extend `GV` with array of symbol spaces, indexed by symbol space accessor.

Array must by dynamic - symbol spaces are dynamic.

#### possible refactoring - internal symbols to symbol spaces

Change `GV` from having enumerated list of values (`gp_sv`, `gp_io`, ...)
into list of value arrays, indexed by symbol table index.

So for example `GvCV` will be changed to
```
#define GvCV(gv) ((CV*)GvGP(gv)->gp_symbol_spaces[SYMBOL_SPACE_SUB])
```

## pad

TODO: I do not understand it enough yet to be able to describe changes

## C API

### `register_symbol_space`

Register new symbol space.

Returns:
- internal symbol space accessor
  internal symbol space accessor is an index to `GV`'s array

Parameters:
- `symbol space name`
  - unqualified `BAREWORD`, must be unique or `NULL`
  - symbol will be used as an import/export attribute
  - when `NULL`
    - symbol space can be addressed from Perl code only by package name
- `symbol space package`
  - must be unique
- `custom grammar` how to parse symbol declaration expression
  when `NULL`, current perl grammar will be used
  custom grammar should return populated symbol (?)
  (see also idea [Exact use VERSION](../../exact-use-version.md))
- function pointer to free this symbol

#### `register_symbol_space_attribute`

Mechanism to attach behaviour represented by independent symbol space
to proper context - moved out into [Attributes workflow](../../attributes-workflow/README.md)

For example:
```
use Data::Contract
    := attribute => contract
    ;

my $var
    := contract => Foo | Bar
    ;
```

Will attach proper behavioural magic to `$var`.
Details moved out into [Full-scale magic](full-scale-magic/README.md)

Registers symbol space attribute and it's handler.

Parameters:
- `accessor`
  - id of symbol table
- `attribute`
  - name of attribute
  - must by unqualified identifier
  - if NULL, `symbol space name` will be used
- `context`
  - in which context it will be applied
- `handler`
  - code executed to process this attribute
- `grammar`
  - how to parse attribute value
  - if `NULL` or missing, symbol space grammar will be used

