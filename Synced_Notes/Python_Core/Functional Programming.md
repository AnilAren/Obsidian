
# Lambda

```
square = lambda x: x * x
print(square(4))  # 16

# A*B
multiply = lambda a,b :a*b
print(multiply(4,4)) # 16
```

# Map


Applies a function to each element.
map(function, iterable)

we can pass multiple iteratable but map will stop after the shortest one gets completed

```
nums = [1, 2, 3, 4]
squares = list(map(lambda x: x**2, nums))
print(squares)  # [1, 4, 9, 16]


input = "1,2,3"
listed =  list(map(int,input.split(",")))
print(listed) # [1, 2, 3]

```


# Filter

**`filter()`** filters elements from an iterable based on a condition.

```
filter(fucntion,iteratable)
```

It keeps only those elements for which the given `function` returns `True`.  

The result is a **filter object (an iterator)**, so you typically need to convert it to a `list` or `tuple` to view the results.

```
nums = [1, 2, 3, 4]
evens = list(filter(lambda x: x % 2 == 0, nums))
print(evens)  # [2, 4]

```

# Reduce

Reduces a sequence into a single value (from `functools`).

Your lambda must still take **exactly two arguments**, because `reduce()` always passes two at a time — the accumulated value and the next item.

```
from functools import reduce
nums=[1,2,3,4]
product = reduce(lambda a, b: a * b, nums) 
print(product)  # 24
```

# ZIP

Combines multiple iterables element-wise.

```
names = ['Anil', 'Raj'] 
scores = [90, 85] 
print(list(zip(names, scores)))  # [('Anil', 90), ('Raj', 85)]
```

```
names = ['Anil', 'Raj'] 
scores = [90, 85, 32]
print(list(zip(names, scores))) # [('Anil', 90), ('Raj', 85)]
```

# Sorted

 `sorted()` with `key`

Sorts using a function.

```
words = ["apple", "banana", "kiwi"] 
print(sorted(words, key=len))  # ['kiwi', 'apple', 'banana']
```

# fuctools.partial()

`functools.partial()` lets you **"freeze" or pre-fill** some arguments of a function — creating a **new function** with fewer parameters.

Think of it like “customizing” a function for repeated use.

### 🧱 Syntax

```
from functools import partial  
partial(func, /, *args, **keywords)
```

The `/` means that **all arguments to the _left_ of it are _positional-only_**.

- `func` → the original function
- `*args` / `**keywords` → values to pre-fill (bind) to the function


## Examples

### Simple Usage

```
from functools import partial

def power(base, exponent):
    return base ** exponent

square = partial(power, exponent=2)
cube = partial(power, exponent=3)

print(square(5))  # 5**2 = 25
print(cube(2))    # 2**3 = 8
```

🔍 **What happened?**

- `partial(power, exponent=2)` created a new function where `exponent` is always `2`.
- So `square(5)` really calls `power(5, 2)` internally.

### Pre-filling positional arguments

```
def multiply(a, b, c):
    return a * b * c

double = partial(multiply, 2)  # fixes a=2
print(double(3, 4))  # 2 * 3 * 4 = 24

```

### 💡 Use cases

- Creating specialized versions of generic functions
- Simplifying callbacks in frameworks (e.g., `map`, `reduce`, event handlers)
- Reusing functions with fixed configurations (e.g., setting a base URL, file mode, etc.)

# functools.lru_cache

`lru_cache` (Least Recently Used Cache) is a **decorator** that automatically **caches function results** —  if you call the same function with the same arguments again, Python reuses the previous result **instead of recomputing it**.

The item that **hasn’t been used (accessed) for the longest time** gets removed when the cache is full.

This improves performance for expensive or recursive computations.

🧱 Syntax

```
from functools import lru_cache

@lru_cache(maxsize=128)
def func(...):
    ...
```

- `maxsize`: how many recent calls to remember (default = 128).  
    Use `maxsize=None` for unlimited cache.
- Automatically uses function arguments (must be hashable, e.g., ints, strings, tuples) as cache keys.
- `maxsize` means the **number of entries (cache items)**, not their size in memory.
- `maxsize=128` → cache up to 128 distinct function argument combinations.

## Examples

### Simple memoization

```
from functools import lru_cache

@lru_cache(maxsize=3)
def add(a, b):
    print(f"Computing {a}+{b} ...")
    return a + b

print(add(2, 3))  # first call → computed
print(add(2, 3))  # second call → cached

```

Output:

```
Computing 2+3 ...
5
5
```

Notice that the second call didn’t print “Computing...” — because it reused the cached result.

### Perfect for recursion (e.g., Fibonacci)

```
from functools import lru_cache

@lru_cache(maxsize=None)
def fib(n):
    if n < 2:
        return n
    return fib(n - 1) + fib(n - 2)

print(fib(35))  # Much faster than naive recursion

```

Without caching, this recursion explodes in complexity.  
With `lru_cache`, each value of `fib(n)` is computed **only once**.

### Inspecting cache

```
fib.cache_info()
# returns: CacheInfo(hits=33, misses=36, maxsize=None, currsize=36)

fib.cache_clear()  # clears the cache
```

- hits - Number of times the cache _was used_ instead of recomputing.
- misses - Number of times a result was _not found_ in the cache and had to be computed.
- maxsize - The cache capacity (how many entries can be stored).
- currsize - The number of entries currently stored in the cache.

|   |
|---|
||
### 💡 Use cases

- Expensive or recursive functions (e.g., dynamic programming)
- Database or API calls with repeated arguments
- Caching computations in data pipelines or ML preprocessing