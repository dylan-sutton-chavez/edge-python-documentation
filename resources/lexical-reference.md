---
title: "Lexical Reference"
description: "Formal specification of Edge Python lexical grammar: token definitions, indentation model, and encoding rules."
---

## Tokens Definition

Token set derived directly from Python `token` and `keyword` modules. To update the syntax after any Python upgrade, new tokens require changes to `lexer.rs` and its wrappers.

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
