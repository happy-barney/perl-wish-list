
# Follow-ups

Possible extensions

## sub local attributes

Using [Test::YAFT](https://metacpan.org/pod/Test::YAFT)'s `it` as an example

```
it q (test title)
    := got => $value
    := got => { block to eval }
    := expect => ...
    := arrange => foo => bar
    ...
```

Signature may look like
```
sub it (
    $title
        := positional
        := required
        := contract  => Single_Line_Text_Without_TAP_Directives
    got
        := required
        := attribute => $custom_grammar_for_got
    expect
        := default   => expect_to_live
        := attribute => $custom_grammar_for_expect
    arrange
        := attribute => $custom_grammar_for_arrange

)
```

```
