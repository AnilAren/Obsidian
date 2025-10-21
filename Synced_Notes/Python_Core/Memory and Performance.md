# 🧠 Python Memory & Performance Optimization Guide

---

## 🧩 Overview

Python is powerful and flexible — but not always the fastest or most memory-efficient language by default.  
Understanding **how Python manages memory** and **how to optimize for performance** can make your code 10–100x faster and more efficient.

---

## 🧠 1. Memory Management in Python

Python handles memory automatically through a **private heap space**, managed by the **Python Memory Manager** and **Garbage Collector (GC)**.

### 🔹 Key Components

| Concept | Description |
|----------|--------------|
| **Object allocation** | Every value (int, string, list, etc.) is an object stored in the heap. |
| **Reference counting** | Python tracks how many references point to an object. When the count hits zero, memory is released. |
| **Garbage Collector (GC)** | Detects and cleans up **cyclic references** (objects referencing each other). Uses **generational GC** for efficiency. |
| **Memory pools (pymalloc)** | Manages small object allocations efficiently, minimizing system calls. |

---

### 🔹 Memory Inspection Tools

| Tool                 | Purpose                                                      |
| -------------------- | ------------------------------------------------------------ |
| `sys.getsizeof(obj)` | Get the memory size (in bytes) of an object.                 |
| `gc`                 | Interact with the garbage collector (e.g., `gc.collect()`).  |
| `tracemalloc`        | Track memory allocations and detect leaks.                   |
| `memory_profiler`    | Line-by-line memory profiling for Python scripts.            |
| `objgraph`           | Visualize object references to detect circular dependencies. |

#### Example: Tracking Memory Leaks
```python
import tracemalloc

tracemalloc.start()

# Your code
data = [x for x in range(10**6)]
del data

print(tracemalloc.get_traced_memory())
tracemalloc.stop()
```

## 2. Performance Optimization Techniques

Performance tuning in Python often means **minimizing overhead**, **choosing efficient data structures**, and **avoiding unnecessary computation**.

## **Techniques**

### 🔹 (A) Data Structure Choices

Use **appropriate data structures**:

- `list` vs `set` → sets are faster for membership checks (`in`).
- `dict` → average O(1) lookup.
- `tuple` → smaller and faster than list if immutability is okay.
- `array` or `numpy` → more memory efficient for numeric data.

Example:

```
import sys
a = [1, 2, 3]
b = (1, 2, 3)
print(sys.getsizeof(a), sys.getsizeof(b))  # list > tuple
```

### 🔹 (B) Avoid Unnecessary Copies

Python containers often create **new objects** when sliced or concatenated.

```
# Inefficient
new_list = old_list[:]  

# Better: iterate or use iterators/generators
for item in old_list:
    ...
```

### 🔹 (C) Use Generators Instead of Lists

Generators don’t store all items in memory.

```
# Bad: consumes memory for 1 million ints
nums = [i for i in range(10**6)]

# Good: generator
nums = (i for i in range(10**6))
```

### 🔹 (D) Built-in Functions and Libraries Are Faster

Use optimized C-based builtins like:

- `sum()`, `min()`, `max()` instead of manual loops
- `map()` and `itertools` for lazy evaluation
- `collections` (like `deque`, `Counter`) for specific patterns

### 🔹 (E) Minimize Attribute Lookups

Repeated dot lookups are expensive.

```
# Bad
for i in range(1000000):
    x.append(i)  # each loop resolves 'append'

# Good
append = x.append
for i in range(1000000):
    append(i)

```

### 🔹 (F) Use Concurrency & Parallelism When Appropriate

- **I/O-bound** → use `asyncio` or threading.
- **CPU-bound** → use multiprocessing or C extensions (e.g., `numba`, `cython`)

### 🔹 (G) Profiling Performance

|Tool|Description|
|---|---|
|`timeit`|Micro-benchmarking small code snippets.|
|`cProfile`|Comprehensive performance profiling.|
|`line_profiler`|Per-line execution time analysis.|

Example:
```
import cProfile
cProfile.run('my_function()')
```

## 🧩 3. **Memory Optimization Tips**

| Technique                                   | Example                                                       |
| ------------------------------------------- | ------------------------------------------------------------- |
| **Use `__slots__` in classes**              | Saves per-instance dictionary overhead.                       |
| **Use `array` or `numpy` for numeric data** | More compact than lists of ints/floats.                       |
| **Del unused objects**                      | `del var` or setting `var = None` helps GC.                   |
| **Batch processing**                        | Process large files in chunks instead of loading entire file. |
| **Memory mapping**                          | `mmap` large files for efficient access.                      |
| **Avoid large global variables**            | Keep data localized to reduce persistence in memory.          |

Please refer [[Utils#__slots__|`__Slots__`]] and [[Utils#`mmap` — Memory-Mapped File Access|mmap]]
## Summary

| Area      | Best Practice                                             |
| --------- | --------------------------------------------------------- |
| Memory    | Use generators, avoid copies, use `__slots__`             |
| CPU       | Use builtins, C extensions, vectorization                 |
| Profiling | Use `cProfile`, `line_profiler`, `memory_profiler`        |
| GC        | Avoid circular refs, clear references manually if needed  |
| Data      | Choose right structures (`tuple`, `set`, `dict`, `array`) |