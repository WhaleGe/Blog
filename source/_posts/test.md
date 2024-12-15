---
title: test
categories:
  - - tool
    - linux
tags:
  - tool
  - linux
---

test

```python
import logging
logging.basicConfig(
    level=logging.DEBUG,
    filename="test.log",
    datefmt="%Y-%m-%d %H:%M:%S",
    format="【%(asctime)s %(levelname)s】 %(lineno)d: %(message)s"
)
logging.debug("debug")
logging.info("info")
logging.warning("warning")
logging.error("error")
```

