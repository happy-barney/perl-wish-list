
# Prototype `$<symbol space>`

In order to be able to work with independent symbol tables we need mechanism
to tell Perl where to look for or where to create symbol.

```
sub with_any_symbol ($<symbol space>);
sub with_new_symbol ($<!symbol space>);
sub with_old_symbol ($<=symbol space>);
```

- mvp: similar to `&`, it will have special effect only when used as first argument
- when parser encounters BAREWORD as first argument of
  function, it will lookup into given symbol space and pass symbol instance
- symbol space can be either registered identifier or package
  - identifier takes precendence if such package exists as well
  - symbol space is resolved at sub compile time

## errors

- when symbol space identifier is not registered yet

## warnings

- `redefine` when prototype with non-existing symbol (`!`) and it exists
- `Undefined <symbol space identifier> symbol` when expect existing (`=`) and it doesn't

