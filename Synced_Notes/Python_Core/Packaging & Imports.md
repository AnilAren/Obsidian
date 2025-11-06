
## 🧩 Topic: **Packaging & Imports**

This covers how Python finds and loads modules and packages — and what **PEP 8** recommends for clean, reliable imports.

---

### 1️⃣ **Absolute vs Relative Imports**

#### ✅ Absolute Import (recommended by PEP 8)

Import using the **full path** from the project’s root package.

```python
# project structure
myapp/
│
├── __init__.py
├── utils/
│   ├── __init__.py
│   └── helpers.py
└── main.py
```

In `main.py`:

```python
from myapp.utils.helpers import some_function
```

✅ **Advantages**

- Clear where the import comes from
- Works reliably even when the package structure changes
- Easy to read and understand

---

#### ⚙️ Relative Import

Uses dots (`.`) to refer to the **current** or **parent** package.

```python
# inside myapp/utils/__init__.py
from .helpers import some_function
```

Here:

- `.` means _current package_
- `..` means _parent package_

✅ Useful when modules move together  
⚠️ Avoid in top-level scripts — it can cause `ImportError` if you run the file directly.

---

### 2️⃣ **What’s the purpose of `__init__.py`?**

> `__init__.py` marks a directory as a **Python package**.

Without it, Python treats the folder as a plain directory, not importable code.

- Before Python 3.3: it was _mandatory_
- Now it’s _optional_, but **still best practice** because:
    - You can add **package-level imports** or setup code
    - Keeps old tooling and linters happy
    - Makes intent explicit: “this folder is a package”

Example:

```python
# myapp/utils/__init__.py
from .helpers import some_function
```

Now you can import directly:

```python
from myapp.utils import some_function
```

---

### 3️⃣ **PEP 8 Guidelines on Imports**

|Rule|Example|
|---|---|
|Use **absolute imports** whenever possible|`from myapp.module import func`|
|Group imports in this order|1️⃣ Standard library → 2️⃣ Third-party → 3️⃣ Local|
|Separate each group with a blank line||
|Avoid wildcard imports (`from module import *`)|Use explicit imports|
|Keep imports at the **top** of the file|Right after module docstring|

Example:

```python
"""Example script following PEP 8 imports."""

import os
import sys

import requests
import numpy as np

from myapp.utils.helpers import some_function
```

---

### 4️⃣ **In a multi-package repo**

When you have a repo like:

```
ai_gateway/
    core/
    adapters/
    router/
```

✅ Use **absolute imports** from the root:

```python
from ai_gateway.adapters.bedrock_adapter import BedrockAdapter
```

❌ Avoid:

```python
from ..adapters.bedrock_adapter import BedrockAdapter
```

because it breaks if the package is imported differently (like in tests or when installed as a library).

---

### ✅ Summary Table

|Concept|Description|Example / Note|
|---|---|---|
|**Absolute import**|Full package path|`from myapp.utils import helpers`|
|**Relative import**|Uses `.` or `..`|`from . import helpers`|
|**`__init__.py`**|Marks folder as a package|Enables `import myapp.utils`|
|**PEP 8 rule**|Prefer absolute imports|Improves clarity and avoids confusion|
