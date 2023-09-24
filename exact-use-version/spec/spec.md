
# Exact use VERSION

Current behaviour of `use VERSION` is based on expectation that
- Perl syntax will not change much
- old syntax will be supported forever

Change behaviour of `use VERSION` from _check minimal version of Perl_
to _behave like this version of Perl_.

Change also allows distribution of Perl grammar as non-core plugin:
- old grammars can be excluded into external libraries
- new grammars for old core can be also distributed as external library

When grammar is distributed as external library, performance can be impacted.

### availability

New behaviour will be automatically available when using `use VERSION`
greater than or equal to implemented version (auto-enabled feature)

### feature exact-use-version

Once on, any `use VERSION` used in scope of validity of feature
will use new behaviour instead of old.

## Proposal

### parser

Implement each version's grammar as pluggable grammar, eg:
- `perl_parser_536`
- `perl_parser_538`
- `perl_parser_540`

Every such grammar will recognize unmatched right curly bracket
as end of document, pushing it back to lexer and pop itself from grammar stack.

`use VERSION;` will act as pluggable keyword, installing proper parser.

Initial parser will recognize:
- `PLUGSTMT` as parsed document (starting with `PL_keyword_plugin = current version`)
- EOF as success
- `}` as unmatched right curly bracket error
- anything else as unexpected token

### opcodes

Grammar may rely on different set / behaviour of opcodes then default set
provided by "current" core.

To handle this we should support opcode remapping or maybe even custom opcodes.

When grammar is loaded it should register opcodes it uses
- `opcode.h` variables cannot be static anymore
- opcodes are identified by name
- opcode names should by unique in time.
  - use suffix (eg: `_540`) to identify when this opcode was introduced.
  - instead of changing opcode behaviour significantly, introduce new opcode instead
  - core (blead minus grammar) should provide only handful set of opcodes (support window)
  - too old and too new opcodes may cause performance penality

`opcode.h` variables should not be static but support registration of custom opcodes

### C API (XS)

C API should follow suffix rule as well

## Benefits

### lowering core maintenance

Backward compatibility of new features doesn't need to be maintain.
We can easily remove keywords, change opcode behaviour - just following
simple suffix rule.

### lowering TCO of using Perl

Proposal splits distribution of new binary / core and distribution of grammar.
That allows easy migration - no unexpected feature when upgrading. Each file
will be parsed and interpreted as it was valid at the point file was created.

### rolling CPAN

Required version will not be an issue anymore.
Perl will just use version specified via `use VERSION` to work with module.

With distributing grammars as shared libraries, we can even use newer grammar
with older interpreter.

Perfect implementation of this will involve update to older perls to support
this behaviour.

## Implementation notes

### multiple bison parsers

Bison allows multiple parser, see [api.prefix](https://www.gnu.org/software/bison/manual/html_node/Multiple-Parsers.html).

We should take similar approach.

### TODO: toke.c refactoring

Even lexer behaviour changes amongst versions with small changes between versions.

`toke.c` should be refactored for better reusability

# Examples

- assume running `v5.38`, following will cause syntax error
```
use feature q (exact-use-version);
use v5.10;

package Foo { }
```

- assume running `v5.10`, following will parse (maybe it will be slower, but will parse)
```
use feature q (exact-use-version);
use v5.14;

package Foo { }
```

## Follow-ups

### `use VERSION BLOCK`

Example (assuming `v5.38`):
```
use v5.10 {
   say q (I'm here);
};

# resuming v5.38 grammar here
```

### `eval := use => VERSION ...`

New attribute on `eval` specifying grammar version it's block is written in.

When given version is not available or in case of parse error, it treats
(as much as possible) its argument as paired expression.

Example:
```
eval := use => v5.40 { ... v5.40 code ... };
```

roughly as shortcut for:
```
eval {
    use feature q (exact-use-version);
    use v5.40;
    ...
}
```

