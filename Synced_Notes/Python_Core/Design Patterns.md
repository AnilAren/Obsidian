
Mostly asked in interviews

| Pattern       | Description                             | Example                                   |
| ------------- | --------------------------------------- | ----------------------------------------- |
| **Singleton** | Only one instance of a class            | Logger                                    |
| **Factory**   | Create objects without specifying class | Model factory                             |
| **Observer**  | One object notifies others              | Event system                              |
| **Decorator** | Add behavior dynamically                | Flask route decorator                     |
| **Strategy**  | Select algorithm dynamically            | Payment processor - Similar to D in SOLID |
| Adapter       |                                         |                                           |
|               |                                         |                                           |

## Factory

```
from abc import ABC, abstractmethod

# Step 1: Create an abstract base class
class Vehicle(ABC):
    @abstractmethod
    def create(self):
        pass

# Step 2: Concrete classes
class Car(Vehicle):
    def create(self):
        return "Car created 🚗"

class Bike(Vehicle):
    def create(self):
        return "Bike created 🏍️"

class Truck(Vehicle):
    def create(self):
        return "Truck created 🚚"

# Step 3: Factory class
class VehicleFactory:
    @staticmethod
    def get_vehicle(vehicle_type):
        if vehicle_type == "car":
            return Car()
        elif vehicle_type == "bike":
            return Bike()
        elif vehicle_type == "truck":
            return Truck()
        else:
            raise ValueError("Unknown vehicle type")

```

### Usage

```
if __name__ == "__main__":
    vehicle = VehicleFactory.get_vehicle("car")
    print(vehicle.create())

    vehicle = VehicleFactory.get_vehicle("truck")
    print(vehicle.create())

```

### Output

```
Car created 🚗
Truck created 🚚
```

## Factory vs adapter

|Pattern|What it does|When you use it|
|---|---|---|
|**Factory**|**Creates** objects — decides _which class_ to instantiate|When you want to centralize object creation|
|**Adapter**|**Connects / translates** between two incompatible interfaces|When you want to make two systems work together|

### 🔸 Factory pattern

The **Factory** chooses **which provider adapter to create**.  
It focuses on **object creation**.

```
class ModelFactory:
    @staticmethod
    def get_model(provider: str):
        if provider == "bedrock":
            return BedrockAdapter()
        elif provider == "vertex":
            return VertexAdapter()
```

So:

```
model = ModelFactory.get_model("bedrock")
```

👉 **Factory = decides which adapter to give you.**


### 🔸 Adapter pattern

The **Adapter** ensures **each provider works the same way** —  
even if their SDKs are totally different.

Example:

```
class BedrockAdapter:
    def generate(self, prompt):
        # internally uses Bedrock API
        ...

class VertexAdapter:
    def generate(self, prompt):
        # internally uses Vertex API
        ...
```

👉 **Adapter = standardizes interface across different providers.**

## 🧠 How they work **together**

✅ This is the best part — in real-world LLM projects (like a Model Gateway):

- You use **Adapter Pattern** to make all providers follow the same interface (`generate()`, `embed()`, etc.)    
- You use **Factory Pattern** (or **ModelRouter**) to decide _which adapter_ to use at runtime.

### 🧩 Combined example

we can have an interface if we want

```
# Step 1: Adapters (make all providers look the same)
class BedrockAdapter:
    def generate(self, prompt):
        return f"[Bedrock response] {prompt}"

class VertexAdapter:
    def generate(self, prompt):
        return f"[Vertex response] {prompt}"

# Step 2: Factory (decides which adapter to create)
class ModelFactory:
    @staticmethod
    def get_model(provider: str):
        if provider == "bedrock":
            return BedrockAdapter()
        elif provider == "vertex":
            return VertexAdapter()
        else:
            raise ValueError("Unknown provider")

# Step 3: Use both together
provider = "vertex"
model = ModelFactory.get_model(provider)  # Factory part
print(model.generate("Hello AI"))         # Adapter part

```

✅ Output:

```
[Vertex response] Hello AI
```



## Strategy

```
from abc import ABC, abstractmethod

# Step 1: Define the strategy interface
class PaymentStrategy(ABC):
    @abstractmethod
    def pay(self, amount):
        pass


# Step 2: Implement concrete strategies
class CreditCardPayment(PaymentStrategy):
    def pay(self, amount):
        print(f"💳 Paid {amount} using Credit Card.")


class PayPalPayment(PaymentStrategy):
    def pay(self, amount):
        print(f"💰 Paid {amount} using PayPal.")


class UPIPayment(PaymentStrategy):
    def pay(self, amount):
        print(f"📱 Paid {amount} using UPI.")


# Step 3: Context class (uses a strategy)
class PaymentProcessor:
    def __init__(self, strategy: PaymentStrategy):
        self._strategy = strategy

    def set_strategy(self, strategy: PaymentStrategy):
        """Change strategy at runtime."""
        self._strategy = strategy

    def process_payment(self, amount):
        self._strategy.pay(amount)

```

### 🧩 Usage:

```
if __name__ == "__main__":
    # Choose strategy dynamically
    processor = PaymentProcessor(CreditCardPayment())
    processor.process_payment(1000)

    # Switch strategy at runtime
    processor.set_strategy(PayPalPayment())
    processor.process_payment(2000)

    processor.set_strategy(UPIPayment())
    processor.process_payment(500)

```

### ✅ **Output:**

```
💳 Paid 1000 using Credit Card.
💰 Paid 2000 using PayPal.
📱 Paid 500 using UPI.
```