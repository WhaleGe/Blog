<!--
 * @Author: huangwaidong@genomics.cn
 * @Date: 2024-12-15 22:09:52
 * @LastEditors: huangwaidong@genomics.cn
 * @LastEditTime: 2024-12-15 23:32:58
 * @Description: 
 * Copyright 2024 by huangwaidong All Rights Reserved.
-->
---
title: test
categories:
  - - tool
    - linux
tags:
  - tool
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
#### Prompt
你是一位科研论文审稿员，擅长写作高质量的英文科研论文。请你帮我准确且学术性地将以下中文翻译成英文，风格与英文科研论文保持一致。
分三步进行翻译工作，并打印每步的结果：
1. 根据中文内容直译成英文，保持原有格式，不要遗漏任何信息
2. 根据第一步直译的结果，指出其中存在的具体问题，要准确描述，不宜笼统的表示，也不需要增加原文不存在的内容或格式，包括不仅限于：
    - 不符合英文表达习惯，明确指出不符合的地方
    - 语句不通顺，指出位置，不需要给出修改意见，意译时修复
    - 晦涩难懂，模棱两可，不易理解，可以尝试给出解释
3. 根据第一步直译的结果和第二步指出的问题，重新进行意译，保证内容的原意的基础上，使其更易于理解，更符合英文科研论文的表达习惯，同时保持原有的格式不变。-输入格式为Markdown格式，输出格式也必须保留原始Markdown格式。这条仅需要回答是否OK,接下来我将给你原文。


