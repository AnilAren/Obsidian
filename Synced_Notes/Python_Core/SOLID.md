
## 🧩 What is **SOLID**?

**SOLID** is an acronym for **five principles** of Object-Oriented Design.  
They make your code:

- ✅ Easy to understand
- ✅ Flexible to change
- ✅ Maintainable over time

---

### 🔠 The five SOLID principles:

|Letter|Principle|Meaning|
|---|---|---|
|**S**|**Single Responsibility Principle (SRP)**|A class should have **only one reason to change**.|
|**O**|**Open/Closed Principle (OCP)**|Software entities should be **open for extension**, but **closed for modification**.|
|**L**|**Liskov Substitution Principle (LSP)**|Subclasses should be **substitutable for their base classes** without breaking functionality.|
|**I**|**Interface Segregation Principle (ISP)**|Don’t force a class to implement methods it doesn’t need. Use **smaller, specific interfaces**.|
|**D**|**Dependency Inversion Principle (DIP)**|Depend on **abstractions**, not on **concrete implementations**.|

---

## 🧠 Let’s break them down with examples 👇

---

### 🧩 **1️⃣ Single Responsibility Principle (SRP)**

> A class should do **one thing only**.

Bad ❌:

```python
class Report:
    def generate(self):
        ...
    def save_to_file(self):
        ...
```

Here `Report` handles **both generating and saving**, which are two different responsibilities.

Better ✅:

```python
class ReportGenerator:
    def generate(self):
        ...

class ReportSaver:
    def save(self, report):
        ...
```

Now each class has **one reason to change**.

---

### 🧩 **2️⃣ Open/Closed Principle (OCP)**

> Code should be **open for extension, closed for modification**.

Bad ❌:

```python
class Discount:
    def get_discount(self, type):
        if type == "silver":
            return 10
        elif type == "gold":
            return 20
```

If you add a new type, you must **modify** the class.

Better ✅:

```python
class Discount:
    def get_discount(self):
        return 0

class SilverDiscount(Discount):
    def get_discount(self):
        return 10

class GoldDiscount(Discount):
    def get_discount(self):
        return 20
```

Now you just **extend** the class — no need to modify existing code.

---

### 🧩 **3️⃣ Liskov Substitution Principle (LSP)**

> A subclass must be able to **replace** its parent without breaking behavior.

Bad ❌:

```python
class Bird:
    def fly(self): pass

class Penguin(Bird):
    def fly(self):
        raise Exception("Penguins can’t fly")
```

You can’t substitute `Penguin` for `Bird` here — breaks LSP.

Better ✅:

```python
class Bird: pass
class FlyingBird(Bird):
    def fly(self): pass
class Penguin(Bird):
    def swim(self): pass
```

Now substitution works correctly.

---

### 🧩 **4️⃣ Interface Segregation Principle (ISP)**

> Don’t make classes depend on methods they don’t use.

Bad ❌:

```python
class WorkerInterface:
    def work(self): pass
    def eat(self): pass

class Robot(WorkerInterface):
    def work(self): pass
    def eat(self):
        raise Exception("Robots don’t eat")
```

Better ✅:

```python
class Workable:
    def work(self): pass

class Eatable:
    def eat(self): pass

class Robot(Workable):
    def work(self): pass
```

Now each class implements only what it needs.

---

### 🧩 **5️⃣ Dependency Inversion Principle (DIP)**

> Depend on **interfaces (abstractions)**, not **concrete classes**.

Bad ❌:

```python
class MySQLDatabase:
    def connect(self): pass

class DataManager:
    def __init__(self):
        self.db = MySQLDatabase()  # tightly coupled
```

Better ✅:

```python
class Database:
    def connect(self): pass

class MySQLDatabase(Database):
    def connect(self): pass

class DataManager:
    def __init__(self, db: Database):
        self.db = db  # loosely coupled
```

Now you can pass any database (Postgres, Mongo, etc.) without changing `DataManager`.

---

### ✅ In short:

|Principle|Keyword|What it ensures|
|---|---|---|
|**S**|One job|Simpler, focused classes|
|**O**|Extend, don’t modify|Easier to add new features|
|**L**|Subclass safely|Reliable inheritance|
|**I**|Split interfaces|Clean, relevant design|
|**D**|Depend on abstraction|Flexible architecture|

---

### 💬 Interview-style short answer:

> SOLID is a set of five object-oriented design principles —  
> Single Responsibility, Open/Closed, Liskov Substitution, Interface Segregation, and Dependency Inversion —  
> which help make code modular, extensible, and maintainable.

---
