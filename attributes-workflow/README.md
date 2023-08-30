
# New attributes workflow

## Motivation

Derived from [independent symbol spaces](../independent-symbol-spaces/README.md)
- declare independent symbol space for attributes
- allow attribute specific value grammar

See [full spec](spec/spec.md)

## Requirements

- syntax should not collide with current capabilities:
  - attributes with value on sub may collide with signatures / prototypes
  - attributes on expressions are currently impossible due conflict with labels
- syntax should not enforce whitespace rules

