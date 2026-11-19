---
title: Python
permalink: /preset/python/
---
## pyrefly.toml

[Pyrefly](https://pyrefly.org/) type checker config. Adjust the paths to where the Python sources live.

```toml
project-includes = ["*.py", "python/**/*.py*", "examples/**/*.py"]
search-path = ["python"]
```

```sh
pyrefly check
```
