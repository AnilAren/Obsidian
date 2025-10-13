
## Quick concepts

- **Exception** = runtime error object thrown with `raise`.
- **Handler** = code in `except` that deals with it.
- **Propagation** = if not handled, exception bubbles up.
- **Chaining** = `raise NewError(...) from exc` keeps original cause.


## Syntax

```
try:
    result = do_work()
except ValueError as e:
    # handle specific error
    logger.error("bad value: %s", e)
except (OSError, IOError) as e:
    # handle multiple related exceptions
    raise  # re-raise after logging if appropriate
else:
    # runs only if no exception occurred
    process(result)
finally:
    # always runs (cleanup)
    cleanup()

```

**Notes**

- `else` is for code that should run only if `try` succeeded (keeps `try` small).
- `finally` is for unconditional cleanup (e.g., close network/socket resources) — but prefer `with` (context managers).
- Use `with` for files, locks, DB transactions, network connections.

## Chaining - Important 
use raise to re-raise same exception
To raise a new exception while preserving cause:
```
try:
    low_level()
except LowError as e:
    raise HighLevelError("higher-level context") from e
```

Using `from` preserves chaining and helps debugging.

### Example

#### without chaining

```
try:
    int("abc")
except ValueError:
    raise RuntimeError("Failed to parse number")
```


Output:
```
RuntimeError: Failed to parse number
```

❌ You lose info about _why_ it failed.
❌ Only the `RuntimeError` is shown.  
You lose the original `ValueError` cause — hard to debug.

#### with chaining

```
try:
    int("abc")
except ValueError as e:
    raise RuntimeError("Failed to parse number") from e

```

Output

```
RuntimeError: Failed to parse number
Cause: ValueError: invalid literal for int() with base 10: 'abc'
```

- The **new** error (`RuntimeError`)
- The **original cause** (`ValueError`)
## Custom exceptions

- Create a custom exception type when you need domain-specific errors (makes handlers specific and readable).
- Inherit from `Exception` (or a more specific built-in)

```
class PaymentError(Exception):
    """Base for payment-related errors."""

class InsufficientFunds(PaymentError):
    pass
```


```
def charge(amount):
    if amount > balance:
        raise InsufficientFunds("not enough funds")

```

