---
type: llm
weight: 2
---

PASS if the final response names at least one Forest package (such as stratiz/Signal), gives its `forest install` command, and asks the user whether to install it or have their own module written.
FAIL if the response mentions no Forest package, or if it delivers a complete Signal implementation without first asking which the user wants.
