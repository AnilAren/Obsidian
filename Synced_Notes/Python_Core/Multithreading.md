

## What is a Thread?

A **thread** is the **smallest unit of [[Utils#Execution|execution]]**** within a process([[Utils#What is a **Process**?]]).

When you run a Python program, it starts with **one main thread** — the one that executes your code.  
But you can create **additional threads** to perform multiple tasks _seemingly_ at the same time.

All threads:
- Belong to the **same process**.
- **Share the same memory space**.
- Can **access the same variables and objects** (but need synchronization to avoid conflicts).


#### Example:
A **thread** is like a **mini-task** that runs **inside a process**.

Think of a **process** as a _factory_, and **threads** as _workers_ inside that factory.  
All workers share the same tools and workspace (memory), but they can work on **different parts of the job at the same time**.

## What is Multithreading?

**Multithreading** means running **multiple threads concurrently** within a single process.

Python uses the `threading` module for this.

💡 However — because of the **Global Interpreter Lock (GIL)** [[Utils#GIL]], only **one thread** executes Python bytecode at a time, even if you have multiple CPU cores(4 CPU cores mean we can run 4 threads simulattenusly).

So, Python multithreading is **concurrent**, not truly **parallel**.

## When is Multithreading Useful?

👉 **I/O-bound tasks** — where the program spends most time waiting (like reading files, calling APIs, or database access).

Because while one thread waits for I/O, another can continue running.
## Key Properties of Threads

| Property                   | Description                                                                                        |
| -------------------------- | -------------------------------------------------------------------------------------------------- |
| **Shared Memory**          | All threads share global variables — but this can lead to race conditions.                         |
| **Lightweight**            | Threads use less memory and start faster than processes.                                           |
| **Synchronization Needed** | You must use locks (`threading.Lock()`) to avoid data corruption.                                  |
| **Single GIL**             | Only one thread runs Python bytecode at a time — true parallelism not achieved for CPU-heavy work. |

## Summary

|Aspect|Description|
|---|---|
|**Definition**|Multiple threads running in the same process.|
|**Parallelism**|Not truly parallel (due to GIL).|
|**Best For**|I/O-bound tasks.|
|**Memory**|Shared among threads.|
|**Risk**|Race conditions, deadlocks if synchronization isn’t handled properly.|
|**Module**|`threading`|