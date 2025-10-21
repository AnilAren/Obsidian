

## **functions are first-class objects**

The term **“first-class”** doesn’t describe _what_ the object is —  
it describes _what you’re allowed to do with it._

A **first-class object** is one that the language treats like a normal value —  
you can:

- assign it to a variable
- pass it as a function argument
- return it from a function
- store it inside a list or dict

That’s what “first-class” means — **no restrictions** on how you use it.

In python we can assign a function to a variable, but in some older languages you can't assign function to a variable.


## What is a **Process**?

A **process** is simply a **program in execution**.

When you run a Python script (`python app.py`), the operating system (OS) creates a **process** — a running instance of that program.

#### Key Characteristics : 

|Property|Description|
|---|---|
|**Independent Execution**|Each process runs in its own environment, isolated from others.|
|**Own Memory Space**|A process has its **own memory** — variables, stack, heap, and system resources.|
|**Has System Resources**|Each process has its own file handles, network connections, etc.|
|**Created by the OS**|The OS handles process creation, scheduling, and termination.|
|**Contains Threads**|A process always has at least one thread (the _main thread_). It can spawn more threads.|
### 🔹 Example (Visual)

If you open:

- 3 Chrome windows → 3 processes (each with its own memory)
- Each process may have multiple threads (e.g., one per tab)

## Execution

“Execution” = **running instructions on the CPU** (line by line, step by step).

When you run a program:

- The **OS** gives the program (process) CPU time.
- Inside that process, **something** actually executes those instructions —  
    → that “something” is the **thread**.

So the **thread** is what actually _runs_ code.

## Concurrent

> **“Execution in overlapping time periods.”**

In other words, tasks may **start, run, and finish at overlapping times**,  
but they don’t necessarily run **at the exact same instant**.

---

### 🧠 How It Works in Python

Even though **only one thread** executes Python code at a time (because of the GIL),  
the Python interpreter **rapidly switches** between threads — many times per second.

This **fast switching (interleaving)** makes it _appear_ that threads are running together,  
but in reality, they’re just taking **turns** very quickly.

---

### ⚙️ Example of Concurrency

|Time|Task A|Task B|
|---|---|---|
|0–1 s|🟢 Running|—|
|1–2 s|—|🟢 Running|
|2–3 s|🟢 Running|🟢 Running _(interleaved)_|
|3–4 s|✅ Done|🟢 Running|
|4–5 s|—|✅ Done|

## GIL

### What the GIL Does

- The GIL is a mutex (lock) that protects access to Python objects
- It prevents multiple threads from executing Python bytecode at the same time
- Only one thread can hold the GIL at any point in time

### When the GIL is Released

The GIL is released during:

1. **I/O operations**: File operations, network calls, database operations
2. **Sleep operations**: When a thread calls `time.sleep()` or waits on synchronization primitives
3. **CPU-bound operations in C extensions**: Some C extensions release the GIL during computation
4. **Periodic releases**: During long-running operations, Python releases the GIL every N bytecode instructions
## Stateless vs statefull

> **Stateless** → Each request is _self-contained_.  
> The server does **not rely on any stored context** (session, memory, previous request info).

> **Stateful** → The server **remembers context or session data** from previous interactions.


## Race Condition

A **race condition** happens when two or more tasks (threads or processes)  
**access the same data at the same time**, and at least one of them **writes** to it.

Because their timing overlaps, the final result becomes **unpredictable**.


## Coupling

**Coupling** = how tightly two pieces of code depend on each other.

- **Tight coupling** → Changing one class _forces changes_ in another.
- **Loose coupling** → Classes communicate through _interfaces_, not implementation details.

## Coupling vs Extensibility

**Loose coupling enables extensibility** — they’re not the same thing,  but one **causes** the other.

 -  abstractions creates **loose coupling**, and **loose coupling enables extensibility**.

| Concept            | Definition                                                                                                             | Purpose                                  |
| ------------------ | ---------------------------------------------------------------------------------------------------------------------- | ---------------------------------------- |
| **Loose coupling** | you can achieve Loose coupling by making Classes depend on **abstractions** (interfaces), not concrete implementations | Reduce dependency, make code independent |
| **Extensibility**  | The ability to **add new behavior or features** without modifying existing code                                        | Adapt to change easily                   |

```
class EmailService:
    def send(self, message):
        print("Sending email:", message)

class Notification:
    def __init__(self):
        self.email_service = EmailService()  # <-- depends on concrete class

    def notify(self, message):
        self.email_service.send(message)

```

This design is **tightly coupled**, because:

- `Notification` depends directly on `EmailService`.
- If you change how email is sent, or switch to SMS or Slack, you must modify `Notification`.

With Abstraction

```
from abc import ABC, abstractmethod

class MessageService(ABC):
    @abstractmethod
    def send(self, message):
        pass

class EmailService(MessageService):
    def send(self, message):
        print("Sending email:", message)

class Notification:
    def __init__(self, service: MessageService):
        self.service = service  # depends on abstraction

    def notify(self, message):
        self.service.send(message)

# Usage
service = EmailService()
notification = Notification(service)
notification.notify("Hello!")
```

Now:
- `Notification` depends on an **interface** (`MessageService`), not on a specific class.
- You can easily replace `EmailService` with `SMSService`, `SlackService`, etc., without changing `Notification`.


## __slots__

`__slots__` — Save Memory in Custom Classes

### 🔹 The Problem

By default, every Python object has a **`__dict__`** that stores its attributes.  
This dictionary allows dynamic addition of new attributes — but also consumes **significant memory** per instance (~64–100 bytes or more).

For large numbers of objects, this overhead adds up quickly.
### 🔹 The Solution: `__slots__`

When you define `__slots__` in a class, Python:
- Disables the creation of `__dict__`
- Allocates **fixed slots** for attributes
- Reduces memory usage and speeds up attribute access

```
class Normal:
    def __init__(self, x, y):
        self.x = x
        self.y = y

class Slotted:
    __slots__ = ('x', 'y')
    def __init__(self, x, y):
        self.x = x
        self.y = y

```

```
import sys

n = Normal(10, 20)
s = Slotted(10, 20)

print(sys.getsizeof(n))  # Larger (has __dict__)
print(sys.getsizeof(s))  # Smaller (no __dict__)

```

### 🔹 When to Use `__slots__`

✅ **Use when:**

- You have **many instances** of a class (e.g., millions of lightweight records)
- Attributes are **known in advance**
- You want **faster attribute access** (fixed layout → faster lookup)

❌ **Avoid when:**

- You need dynamic attributes
- You rely on inheritance that adds new attributes dynamically
- You use features like `__weakref__` (unless explicitly added to `__slots__`)

### 🔹 Notes

- If you need weak references, add `'__weakref__'` to slots. (google this weakref)
- `__slots__` doesn’t affect **class-level attributes**, only instance attributes.
- Works best in **data-holder classes** or **ORM-style models**.

## `mmap` — Memory-Mapped File Access

### 🔹 The Problem

When working with large files (e.g., GBs), reading them fully into memory with `open().read()` is **inefficient** and can cause **MemoryError**.

### 🔹 The Solution: `mmap`

`mmap` (Memory Map) lets you **map a file’s contents directly into memory**, so:

- The OS handles reading/writing as needed.
- You can treat the file like a **bytearray** or **string**, but it’s not fully loaded.
- Random access to any part of the file is **fast**.

Example:

```
import mmap

# Open file for reading
with open("large_file.txt", "r+b") as f:
    mm = mmap.mmap(f.fileno(), 0)  # 0 = map entire file
    print(mm[:100])   # Read first 100 bytes
    mm.close()
```

we can also perform editing a file inplace

### 🔹 Benefits

|Advantage|Description|
|---|---|
|⚡ Fast random access|Jump directly to a file offset without reading entire file.|
|💾 Memory efficient|File is not fully loaded — handled by OS paging.|
|🧠 Zero-copy reads|Data stays on disk until accessed.|
|🔄 In-place modification|Modify file content directly without rewriting entire file.|

### 🔹 When to Use `mmap`

✅ **Use when:**
- File size > available memory
- You need **random access** to parts of a large file (logs, datasets)
- You need to **edit** files without reloading them entirely
    
❌ **Avoid when:**
- File is small — overhead may outweigh benefits

- You need to work with **text (UTF-8)** directly — `mmap` works on **bytes**