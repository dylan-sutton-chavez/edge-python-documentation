---
title: "Lexical Reference"
description: "Formal specification of Edge Python lexical grammar: token definitions, indentation model, encoding rules, and lexer safety limits."
---

## Tokens Definition

Token set derived directly from Python `token` and `keyword` modules. To update the syntax after any Python upgrade, new tokens require changes to `lexer.rs` and its wrappers.

F-strings are decomposed into a three-token sequence rather than a single `String` token: `FstringStart → FstringMiddle → FstringEnd`. Tooling that consumes the token stream should handle this sequence explicitly.

```python
"""
Version:
    Python 3.13.12
"""

import logging

logging.basicConfig(
    level = logging.INFO,
    datefmt="%Y-%m-%d %H:%M:%S", 
    format = "[%(levelname)s] (%(asctime)s): %(message)s"
)

from keyword import kwlist, softkwlist
from token import EXACT_TOKEN_TYPES, tok_name

logging.info(kwlist) # Keywords.
logging.info(softkwlist) # Soft keywords

logging.info(list(EXACT_TOKEN_TYPES.keys())) # Operators and Delimitors.

logging.info(list(tok_name.values())) # Token names.
```

## Indentation Model

The lexer emits `Nl` for blank lines, comments, and lines inside brackets. For all other lines, it emits `Newline` followed by `Indent`, `Dedent`, or nothing depending on whether indentation increased, decreased, or stayed the same. Mixing spaces and tabs halts the lexer with `Endmarker`.

## Soft Keyword Disambiguation

`match`, `case`, and `type` can also be used as identifiers. The lexer resolves the ambiguity by checking what follows: if the next token is `(` `)` `]` `:` `=` `,` `Newline` or `EOF`, they are emitted as `Name`.

## Lexer Safety Limits

Based on OWASP standards, the 04:2021 was adapted to prevent asymmetric DoS attacks, using limiters:

| Constant            | Value | Behavior when exceeded      |
|---------------------|-------|-----------------------------|
| `MAX_INDENT_DEPTH`  | 100   | Emits `Endmarker`, halts    |
| `MAX_FSTRING_DEPTH` | 200   | Excess `{` silently clamped |

## Integration Tests

Tests live in `lexer_test.rs` and load cases from `cases/lexer_cases.json`. Each case is a pair of `[source, expected_tokens]`, the lexer output is compared directly against the expected token list. A dedicated `test_fstring` test asserts that f-strings always produce the `FstringStart -> FstringMiddle -> FstringEnd` sequence in the correct order.
