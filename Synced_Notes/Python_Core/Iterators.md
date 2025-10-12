- It is an object that lets you loop through elements one by one.

- **Iterator** = <mark style="background: #D2B3FFA6;">interface / concept (anyone can implement it)</mark>

- It  implements two special methods 
	- `__iter__()` --> returns the iterator object itself
	- `__next__()` --> gives you next element or stops iteration
- **Remembers** where it left off in iteration. It remembers the index where it stopped this happens in memory
- 👉 In simple terms:
	- An **iterator is an object you can loop over**, which remembers its position during iteration.


## Example

```
	nums = [1, 2, 3]
	it = iter(nums)     # create iterator from list
	print(next(it))     # 1
	print(next(it))     # 2
	print(next(it))     # 3
	# next(it) → StopIteration
```


Here:
- `it` is the **iterator**
- `[1, 2, 3]` is the **iterable** (source of data)

🔹 **Iterable** = something that can _give you_ an iterator  
🔹 **Iterator** = something that _produces_ items one by one

-  we can create iter and next within a class as well

```
class Counter:
    def __init__(self, n):
        self.n = n
        self.current = 0

    def __iter__(self):          # makes it iterable
        return self              # returns the iterator object (itself)

    def __next__(self):          # makes it an iterator
        if self.current < self.n:
            self.current += 1
            return self.current
        else:
            raise StopIteration

```

```
c = Counter(3)
for i in c:
    print(i)

Output:
1
2
3

```

## How will the Iterator gets next element 

An iterator keeps an internal state in memory — like an<mark style="background: #BBFABBA6;"> index or a pointer</mark> — along with a <mark style="background: #BBFABBA6;">reference to the data source.  </mark>

Each time you call `next()`, Python uses that state to fetch the next item and updates it.  

For <mark style="background: #ADCCFFA6;">lists</mark>, it’s an index;
for <mark style="background: #ADCCFFA6;">dicts/sets</mark>, it’s a hash slot; 
for <mark style="background: #ADCCFFA6;">generators</mark>, it’s the saved function frame.

## is for loop a iterator

<mark style="background: #FFF3A3A6;">❌ The **`for` loop itself is _not_ an iterator.**  </mark>
<mark style="background: #FFF3A3A6;">✅ But it **uses an iterator internally** to loop over an iterable.
</mark>
---
<mark style="background: #FF5582A6;">NOTE:</mark>

A **`for` loop** is not an object at all.  
It’s a **control statement** (syntax) built into Python —  like `if`, `while`, or `try`.

So it:
- Doesn’t have attributes
- Doesn’t have `__iter__()` or `__next__()` methods
- Doesn’t _produce_ values on its own

✅ Instead, it **asks another object (your iterable)** to do the iterating.

For the above counter example:

```
class Counter:
    def __init__(self, n):
        self.n = n
        self.current = 0

    def __iter__(self):
        return self               # returns iterator object (itself)

    def __next__(self):
        if self.current < self.n:
            self.current += 1
            return self.current    # return next value
        else:
            raise StopIteration    # stop when done

```

```
for val in Counter(5):
    print(val)

Output:
1
2
3
4
5

```

