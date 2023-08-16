
# Expression `declared`

Expression to address symbol in specific symbol space.

## Grammar

```
"declared" SYMBOL_SPACE_IDENTIFIER BAREWORD;
"declared" SYMBOL_SPACE_IDENTIFIER scalar-expression;
```

## Warnings / errors

- error _symbol space does not exist_
- warning _symbol doesn't exist_
  - unless used as part of `exists` expression

## Behaviour

Returns `Symbol` instance if symbol exists, `undef` otherwise.

`Symbol` instance is new magic (unclear implementation yet)
- `isa CORE::Symbol_Table` should return `true`
- `isa Symbol::Space::Implementation::Package` should return `true`
- `Scalar::Util::blessed` should return `false`
- `ref` should return implementation package
- should stringify as fully-qualified symbol name
- when used in assign statement, RHS is evaluated as:
  - when starts with brace, perl expression
  - otherwise as registered pluggable grammar expression
- when dereferenced, allows perl value to be attached to symbol
- `exists` should return true if symbol exists in symbol space
  - `exists` should recognize returned value of previous `declared`
- `exists` on dereferenced should return true is such value was stored
- `delete` on dereferenced should delete given symbol value

## Examples

- obtain declared symbols and use them to declare new context symbol
```
my $foo = declared contract Foo;
my $foo = declared Data::Contract Foo;
my $bar = declared contract Bar;

my declare contract effective as { condition ? $foo : $bar };
```

- obtain declared symbol by name
```
my $name = q (Foo);
my $foo = declared attribute $name;
```

- query that such symbol exists
```
if (exists declared contract Foo) { ... }

my $symbol = declared contract Foo;
if (exists $symbol) { ... }

sub foo ($<Foo>foo ) { if (exists $foo) { ... } }
```

