
# Goal

Extend Perl with support context specific (sigil-less) symbols without
conflicts with with other contexts (including Perl's own sigil-less symbols)

Scope of this proposal:
- mechanism to declare independent symbol spaces
- mechanism to declare and use their symbols
- mechanism to employ custom grammar for symbol definition

# Motivation

Idea started grow with [Oshun](https://github.com/Perl-Apollo/oshun)
(to make contract identifiers recognized by Perl but not making conflicts),
swiftly identifying more usecases (either in core or modules).

## Target audience

- any CORE functionality working in it's own domain, eg:
  - attributes
  - shared named regexps
  - smartmatch operator rules

- modules exporting their own domain to be used by others, eg:
  - Oshun project
    - or `Type::Tiny`
    - in examples I will use hypothetical `Data::Contract` to avoid conflicts
      with any real module
  - `Dancer2` and co
    - declare routes
    - declare operations
  - DSL modules

- tooling support
  - eg language server will be able to detect custom symbols without
    incorporating rules for every module introducing information qualified
    as identifier (in module context), for example API's operation name

# Other languages

There are languages supporting separated symbol spaces for some symbols
- Python
  - types and functions / variables
- PostgreSQL
  - tables, indexes, constraints, functions, ...
- Java
  - annotations vs other symbols

Though I'm not aware (yet) of any language exposing this functionality via
C API nor via language constructs.

# Full spec

See [full spec](spec/spec.md) for details.

# Roadmap

Github discussion [Independent symbol spaces - Roadmap](https://github.com/happy-barney/perl-wish-list/discussions/2)

# Synergies

While growing this idea another synergic ideas were excluded into separated documents:

- [Attributes workflow](attributes-workflow/README.md)
- [Full-scale magic](full-scale-magic/README.md)

# Examples (pseudocode to show intention)

## declare data contract

```
declare contract Foo as Array[ max_length (10), item (Int) ];
```

Statement uses its own grammar to declare symbol contract
- declare new data contract using contract custom grammar

- declare attribute
```
declare attribute Foo as
    on CODE   => { ... do something ... }
    on SCALAR => { ... do something ... }
    on ARRAY | HASH => { ... do something ... }
  ;
```

- declare shared named regexp (taken from [PPR](https://metacpan.org/pod/PPR))
```
declare regexp PerlStatementSequence as qr (
    (?>(?&PerlPodSequence))
    (?:
        (?&PerlStatement)
        (?&PerlPodSequence)
    )*+
);

- query declared symbol
```
if ($value ~~ declared constract Foo) {
}
```
