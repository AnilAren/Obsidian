
**Object-Oriented Programming (OOP)** is a paradigm based on organizing code into **classes and objects**.
### Key ideas:
- **Class** → Blueprint (like a template)
- **Object** → Instance of that class
- **Attributes** → Variables inside the class
- **Methods** → Functions defined in the class

## Four Pillars of OOP in Python

### 1. Encapsulation

**Idea:** Restrict direct access to object data — use methods instead.

```
class Account:
    def __init__(self, balance):
        self.__balance = balance  # private variable
    
    def deposit(self, amount):
        self.__balance += amount
    
    def get_balance(self):
        return self.__balance

```

➡️ `__balance` is _name-mangled_ → `_Account__balance`.

In Python, when we use double underscores (`__`) only at the start of a variable name (but not at the end), Python performs _name mangling_.  
This means the variable name is internally changed to `_ClassName__variable`.

For example, if a class `Account` has a variable `__balance`, it can technically be accessed as `_Account__balance`.  
However, doing so is **not recommended**, as it breaks the purpose of encapsulation.

Unlike some other programming languages, Python doesn’t have true private variables — the double underscore is mainly a convention to discourage direct access rather than enforce strict privacy.

### 2. Inheritance

- Inheritance allows a class (child/subclass) to **acquire properties and behaviors** (attributes and methods) from another class (parent/superclass).

**Idea:** Reuse and extend existing class behavior.

```
class Animal:  # <-- Superclass
    def speak(self):
        print("Animal speaks")

class Dog(Animal): # <-- Subclass
    def speak(self):
        print("Dog barks")

Dog().speak()  # Dog barks
```

- Parent class: `Animal`
- Child class: `Dog`
- The child can **override** parent methods.
- The child may or may not have its own `speak` method.  
	If it doesn't, it uses the `speak` method from the `Animal` class;  
	if it does, it uses its own version.  
	Additionally, the child class can have other methods as well.


### 3. Polymorphism

- Polymorphism means **“many forms”** — it allows different classes to define methods with the same name but potentially different behaviors.

**Idea:** Same interface, different behavior.

```
class Cat:
    def sound(self):
        return "Meow"

class Dog:
    def sound(self):
        return "Bark"

for animal in [Cat(), Dog()]:
    print(animal.sound())
    
Output:
Meow
Bark

```

### 4. Abstraction

- **Abstraction** means **hiding the internal details** of how something works and **showing only the essential features** to the user. 

Example:
	The user doesn’t need to know _how_ `make_payment()` is implemented inside `PayPal` or `Stripe`.  
	They only need to know that both classes **inherit** from `PaymentGateway` and therefore **must** have a `make_payment()` method.
	So, the user can simply call `gateway.make_payment(amount)` — without worrying about the internal logic of PayPal, Stripe, or any other gateway.


**Idea:** Hide internal complexity, expose only what’s necessary.

```
from abc import ABC, abstractmethod

class PaymentGateway(ABC):
    @abstractmethod
    def make_payment(self, amount):
        pass

class PayPal(PaymentGateway):
    def make_payment(self, amount):
        print(f"Processing ₹{amount} through PayPal...")

class Stripe(PaymentGateway):
    def make_payment(self, amount):
        print(f"Processing ₹{amount} through Stripe...")


```


User uses:
```
def process_payment(gateway: PaymentGateway, amount):
    gateway.make_payment(amount)

process_payment(PayPal(), 500)
process_payment(Stripe(), 700)

```

You don’t need to know _how_ PayPal or Stripe handle payments internally —  
you just know that any payment gateway must have a `.make_payment()` method.

➡️ `PaymentGateway` = abstract class, cannot be instantiated.

## Important Dunder Methods (Magic Methods)

- **Magic methods** (also called **dunder methods**) are **special predefined methods in Python** that start and end with **double underscores**

These make your classes behave like built-ins.

|Method|Use|
|---|---|
|`__init__`|Constructor|
|`__str__`|Human-readable string|
|`__repr__`|Developer-readable string|
|`__len__`|Used by `len(obj)`|
|`__eq__`|Used by `==`|
|`__lt__`, `__gt__`|Comparison|
|`__getitem__`|Used by `obj[key]`|
|`__iter__`, `__next__`|Make class iterable|
|`__enter__`, `__exit__`|Context manager support (`with`)|
## Inheritance vs Composition

#### Inheritance
One class (child) _inherits_ from another (parent).  
It _is a specialized version_ of that parent.

```
class Animal:
    def speak(self):
        print("Animal makes a sound")

class Dog(Animal):       # Dog IS-A Animal
    def speak(self):
        print("Dog barks")

d = Dog()
d.speak()   # Output: Dog barks
```

**Explanation:**

- `Dog` **is-a** type of `Animal`.
- It _inherits_ `speak()` and overrides it.
- Used when classes share behavior and hierarchy makes sense.

🧠 Rule of thumb:
> Use inheritance when the subclass _should behave like_ the parent class, just more specifically.

#### Composition
One class contains an _instance_ of another class as a field.  
It _uses_ that object to perform actions.

```
class Engine:
    def start(self):
        print("Engine started")

class Car:
    def __init__(self):
        self.engine = Engine()   # Car HAS-A Engine
    
    def drive(self):
        self.engine.start()
        print("Car is driving")

c = Car()
c.drive()
```

**Explanation:**

- `Car` **has-a** `Engine`.
- It doesn’t inherit from `Engine`; it _uses_ it.
- Composition models “part-of” relationships.

🧠 Rule of thumb:

> Use composition when you want to _reuse functionality_ from another class but there’s **no clear hierarchy**.

| Concept     | Example                  | Relationship                      |
| ----------- | ------------------------ | --------------------------------- |
| Inheritance | `Manager is an Employee` | Shares behavior (pay, work hours) |
| Composition | `Car has an Engine`      | Works together, not hierarchical  |

### Should we always call composition class inside init

- Composition is usually called or initiated in the init or somtimes passed in the arg of init

Above example shows that we have initiated Engine() in init over here we are passing it while object creation

```
class Car:
    def __init__(self, engine=None):
        self.engine = engine    # can be injected later

    def drive(self):
        if not self.engine:
            print("No engine found!")
            return
        self.engine.start()
        print("Car is driving")

# Inject dependency externally
my_engine = Engine()
car = Car(engine=my_engine)
car.drive()

```

This gives **flexibility** — you can:

- reuse existing `Engine` objects,
- test with mock engines,
- or switch engine types dynamically.

#### When NOT to call it in `__init__`

Use **external or lazy initialization** when:
- The component is **optional** or **shared**.
- You want **dependency injection** for testing/configuration.
- You want **lazy loading** (create it only when needed).

```
class DatabaseClient:
    def __init__(self):
        self._conn = None  # Not created immediately

    def connect(self):
        if not self._conn:
            self._conn = self._create_connection()
        return self._conn

```

Here, the composition (`_conn`) is **created later** — only when used.


### Car can have different kind of engines

> A `Car` _has an Engine_, but not necessarily the **same kind** of engine. Disel, Petrol, EV

This is where **composition + polymorphism (abstraction)** come together beautifully.
#### Step 1️⃣ — Define an abstract base (common behavior)

```
from abc import ABC, abstractmethod

class Engine(ABC):
    @abstractmethod
    def start(self):
        pass
```


#### Step 2️⃣ — Define specific engine types

```
class PetrolEngine(Engine):
    def start(self):
        print("Petrol engine starting...")

class ElectricEngine(Engine):
    def start(self):
        print("Electric engine starting silently...")

class HybridEngine(Engine):
    def start(self):
        print("Hybrid engine combining power sources...")
```

#### Step 3️⃣ — Use **composition** inside `Car`, but **inject** the engine

```
class Car:
    def __init__(self, engine: Engine):
        self.engine = engine    # Composition (Car HAS-A Engine)

    def drive(self):
        self.engine.start()
        print("Car is driving...")
```

#### Step 4️⃣ — Use it flexibly

```
c1 = Car(PetrolEngine())
c2 = Car(ElectricEngine())
c3 = Car(HybridEngine())

c1.drive()
c2.drive()
c3.drive()
```

Output:

```
Petrol engine starting...
Car is driving...
Electric engine starting silently...
Car is driving...
Hybrid engine combining power sources...
Car is driving...
```

#### 🧩 What Happened Here

| Concept                        | Description                                                                               |
| ------------------------------ | ----------------------------------------------------------------------------------------- |
| **Composition**                | `Car` _has an_ engine (`self.engine = engine`)                                            |
| **Abstraction / Polymorphism** | All engines share a `start()` interface                                                   |
| **Dependency Injection**       | You pass different engine objects at runtime                                              |
| **Result**                     | Flexible, extensible design — no code changes in `Car` needed to support new engine types |
```
Car ──→ Engine (interface) ←── PetrolEngine / ElectricEngine
```
✅ Both sides depend on the interface,


[[Utils#Coupling|Loose coupling]] = `Car` doesn’t care _which_ engine it gets, as long as it fulfills the contract (has a `start()` method). BUT In inheritance, `Car` inherits from `Engine`,  
so if I add a new engine type, I’ll need to modify both the parent (`Engine`) and child (`Car`).

FYI - [[Utils#Coupling vs Extensibility|Extensibility vs Coupling]] - both are not sam
## Summary

| Feature      | Inheritance (“is-a”)            | Composition (“has-a”)                 |
| ------------ | ------------------------------- | ------------------------------------- |
| Relationship | Subclass ↔ Parent               | Object contains another object        |
| Example      | `Dog is an Animal`              | `Car has an Engine`                   |
| Coupling     | Tight (child depends on parent) | Loose (can replace component easily)  |
| Flexibility  | Harder to change                | Easier to extend/modify               |
| Code Reuse   | Through hierarchy               | Through object collaboration          |
| Risk         | Over-inheritance / MRO issues   | Slightly more code but cleaner design |

