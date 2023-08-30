
# New attributes workflow

## `register_attribute`

C API function registering attribute. Should recognize parameters:

- `attribute-name`
  fully qualified attribute name
  `UNIVERSAL::***` for global attributes
- `attribute-context`
  in which context is attribute valid
- `custom-grammar` how to parse attribute value?
  when `NULL`, value is parsed as balanced expression (or perl expression if `{ ... }`)
- `handler` how to handle attribute value?
  when `NULL` result of parse grammar is stored
- `default value` value used when attribute is specified without value
  when `NULL`, representation of `true` is used

## Parse and process attribute

Extend `S_apply_builtin_cv_attribute` to handle registered attributes.
When handler is specified, it is executed immediately.

When attribute is not registered, fallbacks to context default behaviour.

Default behaviour for currently recognized contexts is same as now,
call `MODIFY_<>_ATTRIBUTES`

## Declara attribute

## Statement `declare`

Independent symbol spaces should be extended with default handling
of `discriminator`, eg:

```
declare attribute GET [ CODE ] as do { .... };
```

### Custom grammar

Attributes will be declared using `declare` statement introduced by
[independent symbol spaces](../independent-symbol-spaces/README.md).

Custom symbol grammar will be:
```
list-of-attribute-context-handlers:
    attribute-context-handler *
  | "do" perl-sub-body
  | "do" "nothing"

attribute-context-handler:
    "on" attribute-context "do" perl-sub-body
  | "on" attribute-context "do" "nothing"
  | "on" attribute-context

attribute-context:
    attribute-context-name
  | attribute-context-name "|" attribute-context

attribute-context-name:
    SCALAR
  | ARRAY
  | HASH
  | CODE
  | NAMESPACE
  | SIGNATURE
  | EXPRESSION
  | CODE_CALL
  | QUOTELIKE
  | use
  | require
  | ...

```

Where
- `perl-sub-body` will be parsed with preset signature
  TODO: specify signature
  When specified, it is executed in `BEGIN` phase
- `do nothing` only stores attribute value (`true` if value not specified) for later use
- omiting `do` section is same as `do nothing`
- attribute context can be keyword (see [extended require](../../extended-require/README.md))

## attributes on `use`

```
use-attributes:
    use-attribute*

use-attribute:
    ":=" attribute
  | ":=" attribute "=>" custom-grammar-expression
  | ":=" attribute "=>" "{" perl-expression "}"

attribute:
    attribute-name
  | qualified-attribute-name
  | attribute "[" discriminator "]"

```

- `attribute`
  - attribute name
  - can be fully qualified (to avoid conflicts)
  - can use discriminator (see examples)
- `custom-grammar-expression`
  - expression using registered attribute's grammar
  - tokens `:=`, `,`, and `=>`, when used by grammar, must be wrapped
    by pairing characters. Otherwise they must be treated as end tokens
    - eg: `(:=)`
  - parsed before calling attribute handler
  - cannot start with `{`
  - must be ballanced expression
    - for every open character there must be ballanced close character
      (including apostrophe and double quotes)

`use-attributes`, when used on syntax rule followed by comma should
render such comma optional, eg in signatures

## Mindset behind

Treat everything where attributes can appear as an _instance of class_,
with attributes specifying non-default properties.

## Pros

- doesn't use single `:` (already too overloaded character)
- uses different (optional) token for attribute value
- uses attribute specific grammar to parse attribute value
- requires token `:=` before every attribute name
  - so `grep` / `ack` for attribute usage will be simplier
  - leading token acts like _bullet point_
- allows fully qualified attribute to avoid possible conflicts by using
  multiple modules
- provides standard framework how to parse attribute value
  - value is always parsed in list context
  - supports pluggable grammars
  - supports distinction between pluggable grammar and perl grammar
- doesn't prohibit whitespaces (allows nice aligning)
- can be applied at any point of current grammar

## Attribute context

### `SIGNATURE`

Signature context should be separated from variable contexts.
So far I can see few of signature specific attributes.

- `slurpy`
- `positional`
- `required`
- `requires`
- `attribute`
- `default`

Behaviour of attributes on signatures will be separated document.

## Examples

- declare variable contract, using custom grammar and perl grammar
```
my $foo
    := contract => Int
    ;
my $bar
    := contract => { declared contract Int }
    ;
```

- declare variable contract using fully qualified attribute
```
my $foo
    := Data::Contract::contract => Int
    ;
```

- import symbols from symbol spaces
```
use Foo v3.14
    := attribute => Foo[CODE] Bar
    := constant  => MAX MIN
    := contract  => Int Positive_Int
    := sub       => { map +( qq (${_}_min), qq (${_}_max) ), qw (int positive_int) }
    ;
```

- attributes on quote-like operators (assuming `:` is prohibited as delimiter)
```
$value =~ s
    := modifiers => mxnr
    ( ... )
    ( ... )
    ;
```

- quote-like operator's modifiers as attributes
  (accepts boolean, attribute presented = `true`)
```
$value =~ s
    := multiline
    := extended
    := non_capture
    := return
    ( ... )
    ( ... )
    ;
```

- function local attributes - SQL query function
```
select (
    := table   => foo
    := join    => bar ON (foo.id = bar.foo_id and bar.valid_from > $valid_from)
    := columns => foo.*
    := where   => foo.column BETWEEN $min AND $max
)
```

