
# Built-in symbol spaces

## `array`
## `format`
## `io`
## `hash`
## `scalar`
## `sub`

Symbol space used by Perl itself.

Demonstration only: following statements may be considered equivalent
```
sub foo { ... }
declare sub foo as { ... };

sub foo (&);
declare sub foo as (&);

my $foo = ...;
my declare scalar foo as ...;

format FOO = ...;
declare format FOO as ...;

open FOO ...
declare handle FOO as ...;
```

## `symbol_space`

Space to manage symbol spaces themselves.

Used by `declare` and `declared`.

- symbol space doesn't allow qualification
- symbol space allows `my`, `our`, `state`, `local`

```
declare builtin::symbol_space contract as package Data::Oshun;
```

## `attribute`

New symbol space to manage known attributes
- their relation to symbol spaces (and custom grammar)
- importing/exporting extension
- internal attributes (eg `lvalue`)

Symbol space will be consulted before/instead of calling `FETCH_` or
`MODIFY_<>_ATTRIBUTES`.

```
declare attribute GET as
  on CODE do { }
  on SCALAR call sub_reference
```

More context specifiers to be added
- `NAMESPACE` (`class`, `package`)
- `EXPRESSION`
- `SIGNATURE`

## `format`

Declaring formats as independent symbol table allows to move it into package
- perl5 - will be automatically loaded when format is detected
- perl7 - will require to `use format`

