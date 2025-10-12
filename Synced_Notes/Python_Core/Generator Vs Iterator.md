
- **Iterator** = <mark style="background: #D2B3FFA6;">interface / concept (anyone can implement it)</mark>
  **Generator** = <mark style="background: #D2B3FFA6;">Python’s built-in, optimized implementation of that concept</mark>

- An iterator is any object in Python that implements `__iter__()` and `__next__()`.  
A generator is a special kind of iterator that you create using the `yield` keyword.  
Generators are memory-efficient because they produce items on demand instead of storing the whole sequence in memory.

--> [[Iterators]] - An **iterator is an object you can loop over**, which remembers its position during iteration.

[[Iterators#How will the generator gets next element]]

--> [[Generators]] - Generator is a special type of iterator created using generator function or generator expression,  <mark style="background: #BBFABBA6;">Every generator is an iterator but not every iterator is a generator.</mark>

[[Generators#How will the generator gets next element]]


- <mark style="background: #D2B3FFA6;">BOTH ITERATORS AND GENERATORS ARE LAZY EVALUATION </mark>
		**Lazy evaluation** means:  
		_Don’t compute or fetch a value until you actually need it._
		Both iterators and generators are **lazy** → compute next value **only when needed**.
- Both iterators and generators are lazy, <mark style="background: #FFB8EBA6;">but generators are more efficient </mark>because Python implements them in C — they handle state and `StopIteration` automatically, use less memory, and run faster than custom Python iterator classes.
- All generators follow the iterator protocol ✅
  But not all iterators are created by yield ❌



| Feature            | **Iterator**                                    | **Generator**                                     |
| ------------------ | ----------------------------------------------- | ------------------------------------------------- |
| **Definition**     | Object with `__iter__()` and `__next__()`       | Created using `yield` or generator expression     |
| **Creation**       | Manually using classes or `iter()`              | Automatically via `yield`                         |
| **Memory Usage**   | Can be large; must store data or state manually | Very memory efficient — yields one item at a time |
| **Syntax**         | Must define a class or use built-in iterator    | Simple function with `yield`                      |
| **State Handling** | You manually manage iteration state             | Handled automatically by Python                   |
| **Reusability**    | Often reusable (if recreated)                   | One-time use — gets exhausted after iteration     |
| **Example**        | Custom iterator class                           | Generator function                                |
## Quick Comparison Example

### Custom Iterator:

```
class Counter:
    def __init__(self, n):
        self.n = n
        self.current = 0
    
    def __iter__(self):
        return self
    
    def __next__(self):
        if self.current < self.n:
            self.current += 1
            return self.current
        else:
            raise StopIteration

```


### Equivalent Generator:

```
def counter(n):
    for i in range(1, n+1):
        yield i

```

✅ Both do the same thing,  
but the **generator version is 5× shorter and cleaner**.




## Built-in Lazy Iterators & Tools

These are _also_ iterators/generators under the hood (worth knowing for interviews):

| Module / Function      | Description                                           |
| ---------------------- | ----------------------------------------------------- |
| **`range()`**          | Lazy sequence of numbers (doesn’t store all)          |
| **`enumerate()`**      | Yields `(index, value)` pairs lazily                  |
| **`zip()`**            | Lazily pairs items from multiple iterables            |
| **`map()`**            | Lazily applies a function to each item                |
| **`filter()`**         | Lazily filters items by condition                     |
| **`itertools` module** | Tools like `count()`, `cycle()`, `islice()`, all lazy |
## List Comprehension vs Generator Expression**
	
| Feature                | **List Comprehension**                        | **Generator Expression**                     |
| ---------------------- | --------------------------------------------- | -------------------------------------------- |
| **Evaluation**         | Eager — computes _all_ items immediately      | Lazy — computes _one item at a time_         |
| **Memory usage**       | Stores the full list in memory                | Doesn’t store — yields values on demand      |
| **Return type**        | `list`                                        | `generator` (iterator)                       |
| **Speed (small data)** | Slightly faster (precomputed)                 | Slightly slower (one-by-one)                 |
| **Speed (large data)** | May become very slow (too much memory)        | Much faster — constant memory use            |
| **When to use**        | Small/medium datasets you need multiple times | Large/infinite datasets or stream processing |

##
