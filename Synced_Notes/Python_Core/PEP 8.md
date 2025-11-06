
## 🧩 What is **PEP 8**?

> **PEP 8** = _Python Enhancement Proposal 8_  
> It’s the **official style guide** for writing clean, readable Python code.

Think of it as the **rulebook** for how Python code should _look_, so that everyone writes in the same, consistent style.

---

## 🎯 Goal of PEP 8

- Make Python code **easy to read** and **consistent** across projects.
- Improve **maintainability**, **team collaboration**, and **code quality**.

---

## 🧠 Key Rules (with short examples)

### 1️⃣ **Indentation**

- Use **4 spaces per indentation level** (never use tabs).
    

```python
def greet(name):
    if name:
        print(f"Hello, {name}")
```

---

### 2️⃣ **Line Length**

- Maximum **79 characters** per line.
    
- For docstrings/comments: **72 characters**.
    

---

### 3️⃣ **Blank Lines**

- 2 blank lines before top-level functions or classes.
    
- 1 blank line between methods inside a class.
    

---

### 4️⃣ **Imports**

- One import per line.
    
- Order:
    1. Standard library
    2. Third-party
    3. Local imports
    
- Separate them with a blank line.
    

```python
import os
import sys

import numpy as np

from myapp import utils
```

---

### 5️⃣ **Spaces Around Operators**

✅ Good:

```python
x = a + b
```

❌ Bad:

```python
x=a+b
```

✅ For function arguments:

```python
def func(x, y=10):
    ...
```

---

### 6️⃣ **Naming Conventions**

|Type|Convention|Example|
|---|---|---|
|Variable / function|`lower_case_with_underscores`|`total_count`|
|Class|`CamelCase`|`StudentRecord`|
|Constant|`ALL_CAPS`|`PI = 3.14`|
|Private|`_single_leading_underscore`|`_helper()`|

---

### 7️⃣ **String Quotes**

- Use single or double quotes **consistently**.  
    (PEP 8 doesn’t force one — just stay consistent.)
    

---

### 8️⃣ **Comments**

- Keep comments **clear, concise, and relevant**.
    
- Use **docstrings** (`""" """`) for functions/classes/modules.
    

```python
def add(a, b):
    """Return the sum of a and b."""
    return a + b
```

---

### 9️⃣ **White Space**

✅ Good:

```python
if x == 5:
    print("Hi")
```

❌ Bad:

```python
if(x==5):print("Hi")
```

---

### 🔟 **Naming Boolean values**

Use `is_`, `has_`, `can_` prefixes:

```python
is_valid = True
has_permission = False
```

---

## 🧩 Tools to Enforce PEP 8

You can automatically check or format code using:

|Tool|Purpose|
|---|---|
|`flake8`|Style & lint checker|
|`black`|Auto code formatter (PEP 8 compliant)|
|`isort`|Sorts imports properly|
|`pylint`|Code analysis & style checking|

Example:

```bash
pip install black flake8 isort
black app.py
flake8 app.py
```

---

## 💬 Interview-style answer:

> “PEP 8 is the official Python style guide that defines how code should be formatted — indentation, naming, imports, line length, and more.  
> It ensures readability and consistency across projects.  
> In my projects, I use tools like _Black_ and _Flake8_ to automatically enforce PEP 8 compliance.”

