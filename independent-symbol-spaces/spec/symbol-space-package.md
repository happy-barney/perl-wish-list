
# Symbol space package

Package to define behaviour of independent symbol space.

Should extend CORE package (TODO: details)

Every symbol belonging to symbol space represented by this package
will behave like it's blessed to it.

## Constants

### `allow_local`

### `allow_my`

### `allow_our`

### `allow_state`

Controls whether it is valid to use `declare` statement with given modifier.

When `allow_our` is `false`, also modifier-less `declare` is not allowed.

### `allow_override`

When `true`, it's possible to use `declared` expression as LHS of assignment
statement.

### `allow_declare_qualified_symbols`

When `true`, `declare` statement allows fully qualified symbols.
`true` implies `allow_qualified_symbols` = `true`.

### `allow_qualified_symbols`

When `false`, symbols are unqualified and globally scoped.
When `true`, symbols follow standard Perl's qualification rules.

### `constant_symbols`

When `true`, symbols are treated as constants and can be subject
of constants folding.

### `custom_grammar`

Returns value pointing to custom grammar used to parse symbol definition.
When `undef`, only `{ perl-expression }` is available.
- `declare` statement
- attribute
- `declared` expression assignment

### `smartmatch ($value)`

Sometimes (often?) it make sense for independent symbol space to define
_value matches_ functionality. As far as this term is too vague to generalize
overloading smartmatch operator may be best choice.

Called when rhs of smartmatch operator is symbol of this package.

Example:
```
if ($value ~~ declared contract Foo) ...
```

