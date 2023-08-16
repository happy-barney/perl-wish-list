
# Importing / exporting

## Exporting

### Variant A - exporting attributes

Example:
```
declare attribute foo :exported as ...;
declare attribute foo :exportable as ....;
```

### Variant B - implicit export

- every symbol declared without modifier is exported
- every symbol declared with `our` is exportable

### Variant C - overhaul sub import

Mark `sub import`, `@EXPORT`, `@EXPORT_OK`, `%EXPORT_TAGS` with symbol space
it handles.

While importing symbols, specific `import` function will be executed for
each imported symbol space, seeing only related export variables.

```
our @EXPORT :symbol_space(attribute) = ...;
sub import :symbol_space(attribute) { ... export attributes ...}
sub import :symbol_space(contract) { ... export data contract ... }
```

## Importing

To import symbols from independent symbol tables `use` statement should
be extended with attribute support as well.

As an attribute only symbol space names known to imported module are used

Syntax:
```
"use" PACKAGE_NAME VERSION? import-parameter? ATTRIBUTES...
```

Example: import symbols from multiple symbol tables
```
use Package::Foo v1.2 qw (...)
    :constant # import all constants
    :attribute(Identifier)
    :contract(Foo, Bar) # import enumerated checks
    ;

use Package::Foo v1.2
    :constant # import all constants
    :attribute(Identifier)
    :contract(Foo, Bar) # import enumerated checks
    qw (...)
   ;

use Package::Foo v1.2
    :constant # import all constants
    :attribute(Identifier)
    :contract(Foo, Bar) # import enumerated checks
    :perl(qw (...))
   ;
```

Following examples will use new attribute syntax described in
[Attributes workflow](../../attributes-workflow/README.md)

- Import symbol table and related symbols as well
```
use Data::Contract
    := symbol_space => contract
    := contract     => Int Str Dict
    ;
```

-  Import internal symbol spaces will be introduced
```
use Package::Foo v1.2
    := constant
    := attribute    => Identifier
    := contract     => Foo Bar
    := sub          => :all
    := scalar       => $foo
```

-  Import symbols using fully qualified attributes
```
use My::Contract::Library
    := Data::Contract::contract      => Int Str Dict
    := Context::Singleton::singleton => contract_resolver
    ;
```

-  Import symbols using symbol package
```
use My::Contract::Library
    := Data::Contract::     => Int Str Dict
    := Context::Singleton:: => contract_resolver
    ;
```

