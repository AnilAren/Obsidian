

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


