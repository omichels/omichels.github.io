---
layout: post
title: Using insensitive() in terraform with code example
updated: 2022-09-21
category: posts
---


Iterating over a map which is marked sensitive is impossible in terraform, as terrafrom
will avoid to exposing secret data in its output. You will get this error:

```
Sensitive values, or values derived from sensitive values, cannot be used as for_each arguments.
```

See full [example](https://omichels.github.io/terraform/for_each_sensitive_keys/for_each_sensitive_keys) with source code

