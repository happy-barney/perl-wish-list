
# Perl wish-list

Summary of misc ideas how to improve Perl.

Having such ideas on single place prevents repeating same idea or idea
with known blocker over and over as well as identifying such blockers and
possibly solve them.

It may be treated as "just another PPC" - but with things I'm missing
in PPC process

- compare idea with similar behaviour in other languages as much as possible
  (opportunity for me to learn as well)
- notifying synergies between ideas
- decomposing implementation into "jira-like" granularity
- with discussions
  - each should be prefixed with idea name

# Project support

## Oshun

This repository was inspired by [Oshun project](https://github.com/Perl-Apollo/oshun)
with ideas designed to support its implementation.

- [Independent symbol spaces](independent-symbol-spaces/README.md)
  to store contracts separated from sub names
- [Attributes workflow](attributes-workflow/README.md)
  to allow import/export of contracts
  side effect - it provides mechanism to support postfix notation
- [Full-scale magic](full-scale-magic/README.md)
  to make it possible to check data contracts on assignments

Combination of these ideas provides enough functionality to implement postfix approach.

# Alphabetical list of ideas

## [Attributes workflow](attributes-workflow/README.md)

- avoid disambiguities with labels, prototypes, signatures
- support attribute specific grammar for parsing its value
- support attributes on entities where it is not supported yet
  - expressions
  - function local (valid when function is called)

## [Exact `use VERSION`](exact-use-version/README.md)

Change behaviour of `use VERSION` so it will use only given version's
behaviour in such code.

## [Independent symbol spaces](independent-symbol-spaces/README.md)

Provide standard tools to manage separated symbol spaces:
- with qualification (package::symbol)
- with generic warnings / errors (even compile time)
- with handling of `my`, `local`, `our`, `state`
- with importing / exporing

