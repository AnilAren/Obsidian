
## Problem

Imagine your app handles multiple users at once:
```
def handle_request(user):
    log.info(f"Processing user: {user}")
```
In single-threaded code, this is fine.  
But in an **async or multi-threaded** app, two requests might run at the same time — and both will share the same global variables.

### ❌ Bad approach (global variables) 
```
current_user = None

def handle_request(user):
    global current_user
    current_user = user
    # sleep
    log.info(f"Processing {current_user}")

```

If two requests run at the same time, `current_user` might get overwritten.  
So your logs will mix users — chaos!

## The solution: `contextvars`

The `contextvars` module lets you store variables that are **local to the current context** —  
meaning: _each thread or async task gets its own separate value._

Example;

```
from contextvars import ContextVar

user_ctx = ContextVar("user", default="anonymous")

def process_request(user):
    user_ctx.set(user)
    log_request()

def log_request():
    print(f"Processing request for: {user_ctx.get()}")

process_request("Alice")
process_request("Bob")
```

Output:
```
Processing request for: Alice
Processing request for: Bob
```
Even if these two run concurrently in different contexts (like async tasks),  
each will have its own independent value of `user_ctx`.

### Question? Why cant this be used in local variables

- Local variables are **safe**, but they are **not shared** across functions
	Simple Example:
	```
	async def handle_request(user):
    user_name = user       # local variable
    await process_data()
    print(user_name)
	```

Inside `handle_request`, `user_name` is totally safe.  
But what if another function (like `process_data`) also needs to know who the current user is?

You’d have to **pass `user_name` through every function call** manually:

```
async def process_data(user):
    await save_to_db(user)

async def save_to_db(user):
    print(f"Saving for {user}")

```

That quickly becomes messy — especially in large apps or frameworks like FastAPI where you have middleware, background tasks, and DB layers.

### ❌ Local variables can’t “magically” be seen in other functions

Local variables live only in the stack frame of the function that created them.

So if you want every function (e.g., logging, DB, metrics) to know the current `user_id`,  
you’d need to **pass it everywhere manually** — which is painful and error-prone.



2. Global variables can share data, but unsafe in async
   ```
   current_user = None
   ```
- In async or threaded apps, multiple tasks can overwrite `current_user`.
- So it’s not safe for concurrent requests.

2.  ContextVar = “thread-safe global” or “per-task global”
	`ContextVar` gives you the **best of both worlds**:
	- It’s _global_ (any function can read it, no need to pass arguments).
	- But it’s _isolated per task_ (each async request or thread has its own value).

## Real-world use in logging

You can attach things like:

- `request_id`
- `user_id`
- `tenant_id`
- `trace_id`