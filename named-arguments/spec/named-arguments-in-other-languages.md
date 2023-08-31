
# Named arguments in other languages

## Glossary

There are many ways how to pass arguments

- positional
  all parameters are passed as positional
- per-call
  caller can choose wheter to pass positional parameter using
  positional or named argument syntax call
- forced
  function author forces to use named argument syntax per parameter
- mixed-positional
  caller can use named argument syntax to pass (mark) positional argument
- mixed-tailing
  caller can pass some arguments as positional and some as named
  when passing 1 positional parameter using named argument, all subsequent
  positional parameters must be passed using named arguments syntax
  (ie: positional takes priority)
- mixed-free
  caller can pass any argument as named, mixing then with positional.
  Named arguments are assigned first, then unassigned positional.
- forward
  you can use variables to pass arguments, either bind by same name
  or for example hashes (or what given language supports)

## Short overview of support by other languages

Value filled only if supported by syntax (what I was able to google)

| Language      | named | year | per-call | mixed | forced | forward |
|---------------|-------|------|----------|-------|--------|---------|
| C             |       |      |          |       |        |         |
| C#            | 4.0   | 2010 | yes      | yes   |        |         |
| Java          |       |      |          |       |        |         |
| Kotlin        | yes   | 2016 | yes      | yes   |        |         |
| Perl          |       |      |          |       |        |         |
| PHP           | 8.0   | 2020 | yes      | yes   |        |         |
| Postgresql    | yes   | 2010 | yes      | yes   |        |         |
| Python        | yes   | ?    | yes      | yes   | 3.0.0  | yes     |
| Raku          | yes   | 2015 |          |       | yes    | yes     |
| Rust          |       |      |          |       |        |         |
| Typescript    |       |      |          |       |        |         |
| this proposal | yes   |      | yes      | yes   | no     | yes     |

- named
  - whether dedicated syntax exists (yes / since version)
- year
  - since when named arguments syntax exists
- mixed
  - language supports one or many mixed styles
- forced
  - whether language supports forced named arguments
- forward
  - whether language supports forwarding named argument

## Behaviour

Every example will show how to call function
```
function point ($x, $y, $z)
```

with intended arguments:
- `$x = 1`
- `$y = 2`
- `$z = 3`

### per call

Caller can decide whether to pass all arguments as named or as positional.

- __reorder__
  Named argument syntax call usually allows to adapt order of arguments
  to better express user's intention.

|            | Syntax                 |
|------------|------------------------|
| C#         | `foo(z:3, y:2, x:1)`   |
| Kotlin     | `foo(z=3, y=2, x=1)`   |
| PHP        | `foo(z:3, y:2, x:1)`   |
| Postgresql | `foo(z=>3, y=>2, x=1)` |
| Python     | `foo(z=3, y=2, x=1)`   |


### per arguments

Caller can decide to pass some arguments as named

- __fixed__
  Named argument syntax must be used at same place as if it was positional

|        | Syntax           |
|--------|------------------|
| C#     | `foo(1, y:2, 3)` |
| Kotlin | `foo(1, y=2, 3)` |

- __tailing__
  After positional arguments, rest of arguments can be named (and reordered)

|            | Syntax               |
|------------|----------------------|
| C#         | `foo(1, z:3, y:2)`   |
| Kotlin     | `foo(1, z=3, y=2)`   |
| PHP        | `foo(1, z:3, y:2)`   |
| Postgresql | `foo(1, z=>3, y=>2)` |
| Python     | `foo(1, z=3, y=2)`   |

### forced

Calling convention is choosen by author of function.
Caller has no freedom to choose what suits their code base better.

Example:
declare function with one argument (`x`) required to use named syntax

|        | Syntax              |
|--------|---------------------|
| Python | `def fun (a, *, x)` |
| Raku   | `sub fun ($a, :$x)` |

## Language support details

### C

C doesn't support named arguments.

Workarounds ofthen uses structure initialization

```
typedef struct { char *login; char *password } credentials;
login (credentials c) { ... }
login ({ .login = "my login", .password = "my password"})
```

or macros decomposing such structure:
```
#define login(...) _login (((credentials){__VA_ARGV__}).login, ((credentials){__VA_ARGV__}).password)

_login (char *login, char *password);
login (.login = "my login", .password = "my password");
```

### C++

C++ doesn't support named arguments.

Common workaround involves high granularity classes.
```
class Login;
class Password;

login (Login ("my login"), Password ("my password"));
```

### Java

Java doesn't support named parameters.

Common idiom is to use parameter class:
(example borrowed from https://medium.com/@ivorobioff/named-parameters-in-java-9072862cfc8c)
```
new CarParameters()
    .setName("Ford")
    .setModel("Focus")
    .setPrice(20000)
    .setYear(2011)
    .setColor(BLUE)
```

### Kotlin

[documentation](https://kotlinlang.org/docs/functions.html#named-arguments)

Kotlin doesn't support forced named arguments.

Kotlin supports named, mixed-positional and mixed-tailing call syntax.

### Python

Python supports named arguments (called as `keyword arguments`)
[documentation](https://docs.python.org/3/glossary.html#term-argument)

Python supports forced named arguments.
Python allows caller to choose calling style using tailing named arguments approach.

Python function argumens can be computed from data structures (array or dict)

### PHP

PHP support named and mixed-tailing call syntax.
[documentation](https://www.php.net/manual/en/functions.arguments.php#functions.named-arguments)

### Postgresql

Postgresql supports named and mixed-tailing syntax call.
[documentation](https://www.postgresql.org/docs/current/sql-syntax-calling-funcs.html#SQL-SYNTAX-CALLING-FUNCS-MIXED)

### Raku

Raku supports forced named parameters.
Raku supports forwarding function parameter with same name.

### Rust

Rust doesn't support named arguments.
There is nice proposal though: https://internals.rust-lang.org/t/pre-rfc-named-arguments/16413

### Go

Go doesn't support named arguments.

### Swift

Swift used named arguments by default (called argument labels).
[documentation](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/functions/)

Author of function can suppress argument label (ie: force positional).

Positional / named calling style is forced by author.

Swift also supports different public and private parameter name.
