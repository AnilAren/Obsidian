
## 1. What is I/O Handling?

**I/O (Input/Output)** in Python allows interaction with:
- **Input** → Reading from user or files
- **Output** → Writing to console or files
    
The built-in `open()` function is used for **file handling**.

## ⚙️ 2. Opening a File

```
file = open("data.txt", "mode")
```

| Mode   | Description                         |
| ------ | ----------------------------------- |
| `'r'`  | Read (default). File must exist.    |
| `'w'`  | Write (creates/overwrites).         |
| `'a'`  | Append (creates if not exists).     |
| `'x'`  | Create new file (fails if exists).  |
| `'r+'` | Read and write.                     |
| `'b'`  | Binary mode (e.g., `'rb'`, `'wb'`). |
| `'t'`  | Text mode (default).                |
|        |                                     |
## Examples

### 📖 3. Reading from a File

```
f = open("sample.txt", "r")
data = f.read()        # Reads entire file
line = f.readline()    # Reads one line
lines = f.readlines()  # Reads all lines into list
f.close()
```

Example:
```
with open("sample.txt", "r") as f:
    for line in f:
        print(line.strip())
```

✅ **`with` statement** automatically closes the file (best practice).

### ✍️ 4. Writing to a File

```
with open("output.txt", "w") as f:
    f.write("Hello, Anil!\n")
    f.write("This will overwrite existing content.")
```

To **append** data:

```
with open("output.txt", "a") as f:
    f.write("\nNew line added.")
```


### ⚡ 5. Working with Binary Files

```
with open("image.png", "rb") as f:
    data = f.read()

with open("copy.png", "wb") as f:
    f.write(data)
```

### 🧠 7. Example — Copy a file

```
with open("source.txt", "r") as src, open("copy.txt", "w") as dest:
    for line in src:
        dest.write(line)
```

### ⚙️ 8. Exception Handling in File I/O

```
try:
    with open("config.txt") as f:
        data = f.read()
except FileNotFoundError:
    print("File not found.")
except PermissionError:
    print("Access denied.")
else:
    print("File read successfully.")
```




## 6. File Object Methods


|Method|Description|
|---|---|
|`f.read(size)`|Reads `size` bytes (default: entire file).|
|`f.readline()`|Reads one line.|
|`f.readlines()`|Reads all lines into a list.|
|`f.write(str)`|Writes a string.|
|`f.writelines(list)`|Writes a list of strings.|
|`f.seek(offset)`|Moves cursor to a given byte position.|
|`f.tell()`|Returns current cursor position.|
|`f.close()`|Closes the file.|

##  📦 9. `os` and `pathlib` modules for file operations

**Using `os`:**

```
import os
print(os.getcwd())          # Current directory
print(os.listdir("."))      # Files in current directory
os.remove("old.txt")        # Delete file
os.rename("a.txt", "b.txt")
```

**Using `pathlib`:**

```
from pathlib import Path

p = Path("data.txt")
if p.exists():
    print(p.read_text())
p.write_text("New content")
```

