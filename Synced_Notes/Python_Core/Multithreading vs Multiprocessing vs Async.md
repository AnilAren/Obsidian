
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