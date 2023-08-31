
# Named arguments

## Glossary

As far as usage of words `arguments` and `parameters` is not very consistent,
document will use:

- `parameters` for declared function input variables (ie: function signature)
- `arguments` for actual values assigned to function parameters (invoke function)

## Motivation

Named arguments calling convention usually offers better readability
(especially when function has lots of parameters, many with default value)

Worst function call example from my programming experience was function call
with positional 15 boolean parameters (btw, used language supports named arguments)

See [named-arguments-in-other-languages](named-arguments-in-other-languages.md)
some summary how other languages cope with this problem.

Perl has also sigils, so it will be good to be able to declare and pass
multiple list variables as well. Although some (and I often agree) say
you should not name multiple variables with same name but different sigils,
function parameter syntax should not enforce such best practice.

## Current perl

- common practice is to use hash/hashref to simulate named arguments behaviour
- current Perl doesn't allow multiple list paramaeters (`@` or `%`), only as refs
- current Perl doesn't support other syntax construct as parameters

## Nice to have

Named arguments function call should support

- call syntax different enough to not cause conflicts with existing grammar
- call syntax easy to read
- multiple list parameters
- freedom of user to choose calling style
- freedom of user to choose order of passed named arguments
- forward argument(s) to another function
- capability to bind slurpy parameter directly

## Proposal

For call syntax use syntax structure proposed in
[new attribute syntax](../../new-attribute-syntax/idea-new-attribute-syntax.md)

```
:= variable => value,
:= variable => { expression }
```

Expression is lazy evaluated seeing both function parameters and caller's
context variables.

Using another lazy argument from this expression may cause deep recursion.

To pass hashref value, one should use `+ { ... }`

```
sub point ($x, $y, $z);
# want to call with $x=1 $y=2 $z=3

sub point (1,
    := $y => 2,
    := $z => 3,
);
```

## Behaviour

```
sub foo {
    local %_ = %_;
    $_{'$y'} = 2;
    $_{'$z'} = 3;

    for my $parameter (qw ($x $y $z)) {
        $_{$parameter} = shift
            unless exists $_{$parameter}
    }

    my $x = $_{'$x'};
    my $y = $_{'$y'};
    my $z = $_{'$z'};

    ...
}
```

### freedom to choose calling style and order of named arguments

All of these calls are equivalent:
```
foo 1, 2, 3;
foo 1, := $y => 2, 3;
foo 1, 3, := $y => 2;
foo
    := $z => 3,
    := $x => 1,
    := $y => 2,
    ;
```

### supports multiple list parameters
```
sub bar (@foo, %bar);
bar
    := @foo => (1, 2, 3),
    := %bar => (1 => q (one), 2 => q (two), 3 => q (three)),
    ;
```

- supports multiple values using same name (~ GV)
```
baz
    := $foo => 1
    := @foo => (1, 2, 3)
    := %foo => { map { $_ => $foo } @foo }
    := &foo => sub { ... }
```

### supports direct bind of slurpy parameter

As far as slurpy parameter is just another parameter, its value
can be set using `:= =>` expression.

When set, it's slurpyness is disabled.

### forward arguments to another function
```
sub bar ($x, $y);
sub foo ($x) {
    bar := $x, 1;
}
```

when omiting `=>` section, value of same named variable will be used.

### readability

Metrics:

Human doesn't read letter-by-letter (or in programming, char-by-char).
Our brain usually analyses larger structures - words.
Therefor we need visual anchors to hint a brain. In typical text it's
whitespaces. In programming language it can be special sequence of
punctuation characters as well.

See for example: https://blogs.scientificamerican.com/frontiers-for-young-minds/the-reading-brain/

- using same token to address parameter as it is used in signature
  simplifies visual comparison - brain can treat sigil as part of picture
  it analyses, not to extract it first and then slowly analyse rest
- using whitespace invariant syntax allows alignment - again improved
  visual anchors
- leading punctuation token acts as "bullet point" (good also for `grep` / `ack`)

