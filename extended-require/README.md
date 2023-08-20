
# Extended require

## Motivation

Allow well-known behaviour to be configurable instead of having multiple
ways to do it partially, eg:
- [PPC load_module](https://github.com/Perl/PPCs/blob/main/ppcs/ppc0006-load-module.md)
- [CPAN module require::relative](https://metacpan.org/pod/require::relative)

## Idea

Introduce `require` attributes to alter its behaviour:
- `:module` force parameter to be module name
- `:relative` lookup relative to file's location

See [spec](spec/spec.md) for more.

## Similar support in other languages

### Python

Provides `import` statement and `importlib` library.

- `import` statement
  - similar to `require MODULE` version in Perl
  - uses `.` as module separator
  - uses leading `.`(s) to address relative modules
- `importlib` library
  - provides load at runtime functionality, using same syntax (in text variable)
    as import

### Ruby

Ruby provides functions

- `load`
  - loads file by path or by its name without extension
    - if specified module is not available as exact path, looks for another by adding predefined extensions
  - if file starts with `./`, `../`, it's considered relative to `pwd`
    otherwise uses `$LOAD_PATH`
  - uses `/` as hierarchy separator
  - loads file every time
- `require`
  - similar to `load` but loads file only once
- `require_relative`
  - loads file relative to current file's dir

