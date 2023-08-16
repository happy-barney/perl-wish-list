
# Independent symbol spaces

## Behaviour

Independent symbol spaces will act like Perl symbols (sigil-less) but will
not conflict with symbols with other spaces nor Perl's.

Requirements:
- symbol must be valid Perl identifier
- symbol can be empty (declare anonymous symbol)
- symbol can be fully qualified
  - opt-in on declaration
- symbol should interact with `my`, `local`, and (default) `our`
  - opt-in on declaration
- symbol should be importable / exportable

Example:
```
sub foo { ... }
declare attribute  foo as ...;
declare contract   foo as ...;
declare smartmatch foo as ...;
declare operation  foo as ...;
```

## Proposed changes

### Statement `declare`

Generic statement to declare symbol in independent symbol space.

Example:
```
declare contract Foo as Int | Array[Int];
```

See [statement-declare.md](statement-declare.md) for more.

### Expression `declared`

Generic method to access independent symbols.

Example:
```
my $foo = declared contract Foo;
$foo->validate ($data);
```

See [expression-declared.md](expression-declared.md) for more.

### Prototype `$<symbol space>`

Example:
```
package Foo {
    sub with_symbol ($<Data::Contract>);
    sub with_symbol ($<contract>);
    sub with_new_symbol ($<!contract>);
    sub with_existing_symbol ($<=contract>);
}
```

See [prototype.md](prototype.md) for more.

### Symbol space package

Package representing independent symbol space and its configuration.

See [symbol-space-package.md](symbol-space-package.md) for more.

### Internal symbol spaces

Represent internal Perl's symbols via corresponding symbol spaces.

See [internal-symbol-spaces.md](internal-symbol-spaces.md) for more.

### Internal changes

See [internal-changes.md](internal-changes.md) for more.

### Importing / exporting

Introduce new attributes on `use` and `sub import` allowing
to import/export symbols from different symbol spaces.

See [import-export.md](import-export.md) for more.

### Open questions

[github discussion](https://github.com/happy-barney/perl-wish-list/discussions/3)

## Follow-ups and possible future extensions

See [follow-ups-extensions.md](follow-ups-extensions.md) for more.

