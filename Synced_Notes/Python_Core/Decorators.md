- A decorator is a function that takes another function, adds extra functionality around it, and returns a new function — without changing the original one.  
  
- Using `@decorator` syntax makes it clean, reusable


In Python, **functions are first-class objects** — they can be passed as arguments, returned, or assigned to variables.

## 🧩 Basic Syntax

### Without `@`:

```
def decorator(func):
    def wrapper():
        print("Before function")
        func()
        print("After function")
    return wrapper

def greet():
	"""Say Hello"""
    print("Hello!")

greet = decorator(greet)
greet()

```


Output:

```
Before function
Hello!
After function
```

### With `@decorator`:

```
@decorator
def greet():
	"""Say Hello"""
    print("Hello!")

```

This is same as  ```greet = decorator(greet)```

## Examples

### Logging

```
def logger(func):
    def wrapper(*args, **kwargs):
        print(f"Running {func.__name__}()")
        return func(*args, **kwargs)
    return wrapper

@logger
def say_hello():
    print("Hello Anil!")

```

### Timing

```
import time

def timer(func):
    def wrapper(*args, **kwargs):
        start = time.time()
        result = func(*args, **kwargs)
        end = time.time()
        print(f"{func.__name__} took {end - start:.4f} sec")
        return result
    return wrapper

@timer
def compute():
    sum(range(10**6))

```


##  Decorators with Arguments

```
def repeat(times):
    def decorator(func):
        def wrapper(*args, **kwargs):
            for _ in range(times):
                func(*args, **kwargs)
        return wrapper
    return decorator

@repeat(3)
def greet():
    print("Hello!")

```

Output:

```
Hello!
Hello!
Hello!
```


## we can stack multiple decorators

```
@timer
@logger
def process():
    print("Processing...")
    
```

Order of execution:
1. `process = timer(logger(process))`
2. Innermost decorator (`logger`) runs first

## Preserving Metadata

- When you wrap a function, its name/docstring changes to the wrapper’s.

For above greet example:
if we do 

```
print(greet.__name__)
print(greet.__doc__)
```

```
Output:

wrapper
None
```

Here greet functions doc string "Say Hello" is lost and the greet function name is changed to wrapper which is our decorator.

To Solve this we use `@wraps(func)` from the `functools` module

```
from functools import wraps

def decorator(func):
    @wraps(func)  # this will solve the issue
    def wrapper():
        print("Before")
        func()
    return wrapper

```

```
print(greet.__name__)  # 'greet'
print(greet.__doc__)   # 'Say hello'
```

So `@wraps(func)` **copies the metadata** of the original function (`__name__`, `__doc__`, etc.)  
to the wrapper — that’s why we always use it inside decorators.

### What is the significance of these `__name__` or` __doc__`?

These are **special attributes** every function (and many objects) in Python have —  
used mainly for _introspection_, _debugging_, and _documentation_.

**Why it’s useful:**

- Used in logs, debuggers, and stack traces
- Helps identify which function ran

You get meaningful output like `Running function: greet` instead of `Running function: wrapper`.


## Decorators can also be used on Methods

```
def log_method(func):
    def wrapper(self, *args, **kwargs):
        print(f"Method {func.__name__} called")
        return func(self, *args, **kwargs)
    return wrapper

class Example:
    @log_method
    def run(self):
        print("Running...")

```

OR

```
class logger:
	def log_method(func):
	    def wrapper(self, *args, **kwargs):
	        print(f"Method {func.__name__} called")
	        return func(self, *args, **kwargs)
	    return wrapper
```

```
import logger
class Example:
    @logger.log_method
    def run(self):
        print("Running...")
```

## Summary

|Concept|Description|Example|
|---|---|---|
|**Decorator**|Function that wraps another|`def dec(func): return wrapper`|
|**@decorator**|Syntactic sugar|`@timer`|
|**Nested decorators**|Multiple layers|`@timer @logger`|
|**With arguments**|Decorator factory|`@repeat(3)`|
|**Preserve metadata**|Use `@wraps(func)`|From `functools`|
|**Applied to**|Functions, methods, or classes|`@classmethod`, `@staticmethod`|