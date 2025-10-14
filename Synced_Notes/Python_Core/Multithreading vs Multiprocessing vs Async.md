
|Feature|**Asyncio**|**Threading**|**Multiprocessing**|
|---|---|---|---|
|Execution|1 thread, many tasks|Many threads|Many processes|
|Parallel?|❌ No (one CPU at a time)|🟡 Not true parallel (GIL)|✅ True parallel|
|Good for|I/O-bound tasks|I/O-bound tasks|CPU-bound tasks|
|Overhead|Very low|Medium|High|
|Typical use|API calls, DB queries, network servers|Same|Computation-heavy work|

---

## Async vs Multithreading

|Concept|**Multithreading**|**Async / asyncio**|
|---|---|---|
|**Who handles switching?**|OS (real threads)|Python’s event loop|
|**How many threads?**|Many|One|
|**Parallel?**|Not truly (GIL)|No (only 1 thread)|
|**Type of waiting handled well**|I/O (network, disk, etc.)|I/O (network, disk, etc.)|
|**Overhead**|Medium|Very low|
|**Best for**|Code that waits a lot (I/O-bound) and can benefit from multiple threads|Many I/O tasks where thread overhead would be wasteful|

Both solve the same problem (not wasting time waiting),  
but **async** does it in one thread, with cooperative scheduling instead of real threads.

|Concept|Async|Threading|
|---|---|---|
|**Switching control**|Python decides (when hitting `await`)|OS decides (schedules threads anytime)|
|**Data safety**|No shared-memory issues (one thread)|Shared memory → need locks|
|**Context switching cost**|Very low|Higher|
|**CPU work**|Not parallel|Not parallel (GIL)|
|**I/O work**|Efficient|Efficient but heavier|


---
## If you look both async and multithreading are same its just that async uses only one thread also the thread lock is not needed as memory is shared properly...

Coroutines happen only in async....

 - Each coroutine in async has its **own independent local variable**, stored on its **own call stack**, so no cross-contamination happens. 
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

Problem is not threads its concurrency. <mark style="background: #BBFABBA6;">Coroutines share same global variables.</mark>