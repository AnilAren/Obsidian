
  **Generator** = <mark style="background: #D2B3FFA6;">Python’s built-in, optimized implementation of that concept</mark>
  
-  It is a special type of iterator created using
	- generator functions - functions with yield keyword
	- generator expressions - list comprehensions but with parentheses

- <mark style="background: #BBFABBA6;">Every generator is an iterator but not every iterator is a generator</mark>



Generator Function:
	
```
def count_up_to(n):
    i = 1
    while i <= n:
        yield i       # yields instead of return
        i += 1

counter = count_up_to(3)
print(next(counter))  # 1
print(next(counter))  # 2
print(next(counter))  # 3
```

- Each `yield` pauses the function.
- The next `next()` call resumes from where it left off.
- Automatically implements `__iter__()` and `__next__()`.

Generator Expression

```
gen = (x * x for x in range(3))
print(next(gen))  # 0
print(next(gen))  # 1
print(next(gen))  # 4
```

### How will the generator gets next element 

For generators, “next” is not based on data structure — it’s based on **where the function paused**.

When a generator yields:
1. Python **freezes** that function’s frame:
    - Local variables (their memory)
    - The current line number (instruction pointer)
    - The call stack info
2. On the next `next()` call, Python **restores** that frame and continues from the last `yield`.
    

✅ So for generators:
- There’s no “next element” in memory yet.
- “Next” means “resume execution from the previous yield point.”


