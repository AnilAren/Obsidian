## What is **multiprocessing** (short)

**Multiprocessing** runs multiple **processes** (independent OS-level programs), each with its **own Python interpreter** and **own memory**.  
Because each process has its own GIL, CPU-bound Python work can run **truly in parallel** across cores.

## Example

```
# cpu_pool.py
import multiprocessing
import time
import os

def heavy(n):
    # some CPU-heavy work
    s = 0
    for i in range(10_000_000):
        s += (i % (n + 1))
    return (n, s, os.getpid())

if __name__ == "__main__":
    nums = [1,2,3,4]  # 4 tasks
    t0 = time.perf_counter()
    with multiprocessing.Pool(processes=4) as pool:
        results = pool.map(heavy, nums)
    t1 = time.perf_counter()
    print("Results:", results)
    print("Elapsed:", t1 - t0)

```

Output:
```
Results: [(1, 49999995, 23510), (2, 99999990, 23511), (3, 149999985, 23512), (4, 199999980, 23513)]
Elapsed: 2.12
```

(Each tuple shows `(n, sum, pid)` — different PIDs = different processes.)

**What this shows:** tasks ran in parallel on 4 processes → wall time close to the time of the longest single task, not sum of all.

#### Sequential (single-process) equivalent

```
# cpu_seq.py
import time
import os

def heavy(n):
    s = 0
    for i in range(10_000_000):
        s += (i % (n + 1))
    return (n, s, os.getpid())

nums = [1,2,3,4]
t0 = time.perf_counter()
results = [heavy(n) for n in nums]
t1 = time.perf_counter()
print("Results:", results)
print("Elapsed:", t1 - t0)

```

```
Results: [(1, 49999995, 23510), (2, 99999990, 23510), (3, 149999985, 23510), (4, 199999980, 23510)]
Elapsed: 8.41

```

Same work, single PID, roughly ~4× slower (depends on cores).

## Why multiprocessing is faster for CPU-bound tasks

- **No single GIL** restriction per process — each process has its own GIL → true parallel CPU use.
- CPU-heavy loops can execute on different cores simultaneously.
- **Result:** near-linear speedup up to number of physical cores (minus overhead).

## How can we share data between these multiple process which have different memory space


Each **process** has its **own memory**,  
so normal variables aren’t shared.

If process A changes a variable,  
process B **won’t see it** — unless you use something **shared**.


1. Use a **Queue** 
2. Use a **Manager**
3. Use a **Value** (for one shared number)

Please ask gpt too much info

but its better to use external services like redis, database , s3 buckets ...




