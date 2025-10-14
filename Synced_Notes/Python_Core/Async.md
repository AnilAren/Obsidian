
## **Async** means:

> Do other work **while waiting** — without creating new threads or processes.

It’s still **one process** and **one thread**,  
but the program can **pause** one task and **switch** to another while waiting for something (like I/O).

### Simple way to think of it

You tell Python:

> “When you’re waiting (for a file, network, or sleep), go do something else.”

So it’s like **multitasking in one thread** — controlled by Python instead of the OS.


### Example

#### normal (blocking) code

```
import time

def download(n):
    print(f"Start {n}")
    time.sleep(2)
    print(f"Done {n}")

def main():
    for i in range(3):
        download(i)

main()

```

Output:

```
Start 0
Done 0
Start 1
Done 1
Start 2
Done 2
```

⏱ Takes about **6 seconds** (2s × 3).

#### same with **asyncio**

```
import asyncio

async def download(n):
    print(f"Start {n}")
    await asyncio.sleep(2)
    print(f"Done {n}")

async def main():
    tasks = [download(i) for i in range(3)]
    await asyncio.gather(*tasks)

asyncio.run(main())
```

Output:

```
Start 0
Start 1
Start 2
Done 0
Done 1
Done 2
```

Takes about **2 seconds total** —  
all three downloads _overlap_ while waiting for `sleep(2)`.


### What’s happening here

| Concept         | Meaning                                                                   |
| --------------- | ------------------------------------------------------------------------- |
| `async def`     | defines a coroutine (an async function)                                   |
| `await`         | means “pause here until this finishes, and let other tasks run meanwhile” |
| `asyncio.run()` | starts the event loop that manages all tasks                              |

##  What is Coroutine

A **coroutine** is just a _special kind of function_ that can **pause** and **resume** itself.

You create one with `async def`.

#### Example:

```
async def work():
    print("Start")
    await asyncio.sleep(2)
    print("End")

```

Here:

- `work()` → doesn’t run immediately; it creates a **coroutine object**.
- `await asyncio.sleep(2)` → tells Python:  
    “pause me for 2s, and let something else run in the meantime.”

- Coroutine runs when:
	- you `await` it, or
	- you pass it to `asyncio.run()` / `asyncio.gather()`.
	
- `await` means “pause this coroutine until another async task is done”.
	- When you `await`:
		1. The coroutine **pauses** at that point.
		2. <mark style="background: #D2B3FFA6;">A coroutine **yields control** (lets the event loop run something else)  only when it **hits an `await`** on something that is **awaitable**.</mark>
		   - If an await is done on a computational complex function it does not do async as there is nothing to await for, for async to work we need to await on awaitable operation.
		1. When the awaited task completes, the paused coroutine **resumes** right after the `await`.
- Each coroutine has its **own independent local variable**, stored on its **own call stack**, so no cross-contamination happens.

## what is event loop? 
- **Event loop:** the scheduler that keeps track of paused tasks and resumes them when ready.

> The **event loop** is the “manager” that runs and switches between coroutines.

- It keeps track of all tasks (coroutines) that are **waiting** or **ready to run**.
- When one coroutine hits an `await`, it **pauses** and the loop runs another coroutine that’s ready.
- When the awaited operation finishes, the loop **resumes** the paused coroutine from where it left off.

<mark style="background: #BBFABBA6;">- Think of the event loop as a **single-threaded scheduler** that gives each coroutine a turn.</mark>
## How is it different from normal execution

Waiting means the task is **not using the CPU** — it’s waiting for some **external operation** to finish:

- waiting for data from the internet (API call)
- waiting for a file read/write
- waiting for a timer (`sleep`)

Normally, that would block the whole program.  
But in async, while one coroutine _waits_, the event loop **runs another coroutine**.


## If you look both async and multithreading are same its just that async uses only one thread also the thread lock is not needed as memory is shared properly...

 - Each coroutine has its **own independent local variable**, stored on its **own call stack**, so no cross-contamination happens. 
	  - If you call the same coroutine twice, each call has separate memory for its local variables
	  -  Every time you call a **coroutine function**, Python creates a **new coroutine object** — similar to how calling a normal function creates a new stack frame.
	- Each coroutine object has its **own local scope and variables**, so data inside one coroutine doesn’t affect another.
	- Even if two coroutines are created from the same function, they don’t share local variables.

- difference 

| In multithreading                                                                                          | In asyncio                                                                                                           |
| ---------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------- |
| Multiple threads run **at the same time** and share the same memory → need **locks** to protect variables. | Only **one task runs at a time** (in one thread) → no simultaneous access → no race conditions.(for local variables) |
	- So even if many async functions “exist”, only one of them is _executing_ at a given moment.  
	Python switches between them **only at `await` points**, where you’ve explicitly said: “it’s safe to pause here”.
	That’s why:
		- No race conditions
		- No need for `Lock()`
		- No corrupted shared state
	It’s **cooperative multitasking**, not preemptive like threads.


- Even though it runs in single thread there will be clash between the global variables (Local variables are safe )
	- Even though async is **single-threaded**, it’s still **concurrent**.
		That means:
		- Two coroutines **interleave** execution.
		- They share the same memory (globals), but switch back and forth at every `await`.
Example:

```
import asyncio

current_user = None

async def handle_request(user):
    global current_user
    current_user = user
    await asyncio.sleep(1)
    print(f"Handled by: {current_user}")

async def main():
    await asyncio.gather(
        handle_request("Alice"),
        handle_request("Bob"),
    )

asyncio.run(main())

OUTPUT:
Handled by: Bob
Handled by: Bob
```

So when `handle_request("Alice")` does `await asyncio.sleep(1)`,  
it _pauses_, and `handle_request("Bob")` runs and sets `current_user = "Bob"`.  
When Alice resumes, the global now points to `"Bob"` — boom, wrong value.

Problem is not threads its concurrency. Coroutines share same global variables.
## Where is the data stored while it’s waiting?

When a coroutine hits `await`, Python:

1. Saves its **state** (local variables, position in the code, call stack frame).
2. Marks it as “paused”.
3. Gives control back to the **event loop**.

This saved state lives in memory (inside the coroutine object).  
When the awaited I/O completes, the event loop **restores** that state and resumes the coroutine from where it left off.

So no threads, no lost data — just paused and resumed functions.\

## Async without I/O

If your async functions **don’t actually wait for anything**,  
`async/await` adds **no concurrency or speedup** — it behaves exactly like normal sequential code.

```
import asyncio

async def add(a, b):
    print(f"Adding {a} + {b}")
    return a + b

async def main():
    x = await add(1, 2)
    y = await add(3, 4)
    print(x, y)

asyncio.run(main())

```

Output:

```
Adding 1 + 2
Adding 3 + 4
3 7
```

➡ Runs line-by-line, just like normal Python.  
No overlap happens because there’s no `await` that releases control (like `asyncio.sleep()` or I/O call).

So **async only helps** when your functions _yield control_ by awaiting something that **takes time externally** (network, file, DB, timer).

## Summary

|Concept|Meaning|
|---|---|
|**Coroutine**|An async function that can pause and resume (is an object).|
|**Event loop**|The scheduler that runs coroutines and resumes them later.|
|**Await**|“Pause here until result comes, let someone else run.”|
|**Race condition**|Two tasks changing the same data at the same time → unpredictable result.|
|**Async vs Thread**|Async: 1 CPU, cooperative switching. Thread: multiple threads, OS switching.|
|**Async without I/O**|Runs like normal sequential code, no gain.|
