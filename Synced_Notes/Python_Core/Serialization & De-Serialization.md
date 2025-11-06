
Please take a look at [[Serialization & De-Serialization#DUMP VS DUMPS]]
### 🧠 1. What is Serialization?

**Serialization** means converting a **Python object** (like a list, dict, or custom class) into a **format that can be stored or transmitted** — such as:

- writing to a file
- sending over a network
- saving in a database

The reverse process is called **Deserialization** (or **unpickling**).

> **In short:**  
> 🗂️ _Serialization = Python object → byte stream / JSON string_  
> 🔁 _Deserialization = byte stream / JSON → Python object_



### ⚙️ 2. Common Serialization Modules

|Module|Format|Human-readable|Use case|
|---|---|---|---|
|`pickle`|Binary|❌|Python-specific data storage|
|`json`|Text|✅|Data interchange (APIs, configs)|
|`marshal`|Binary|❌|Internal Python use only|
|`yaml` (via `PyYAML`)|Text|✅|Config files|
|`msgpack`|Binary|❌|Fast, cross-language serialization|
## 🧩 3. Using `pickle` — Python’s built-in binary serializer

### ➤ Serialize (Pickle) an object

```
import pickle

data = {"name": "Anil", "age": 28, "skills": ["Python", "ML"]}

# Serialize to file
with open("data.pkl", "wb") as f:
    pickle.dump(data, f)
```

### ➤ Deserialize (Unpickle)

```
with open("data.pkl", "rb") as f:
    loaded_data = pickle.load(f)

print(loaded_data)
# {'name': 'Anil', 'age': 28, 'skills': ['Python', 'ML']}
```

### ➤ Serialize to bytes directly

```
serialized = pickle.dumps(data)   # -> bytes
original = pickle.loads(serialized)
```

✅ **Advantages:**

- Handles almost any Python object
- Easy to use

⚠️ **Caution:** Never `pickle.load()` untrusted data — it can execute arbitrary code.

| Function                     | Output type                            | Used for                                                                   | Typical usage                               |
| ---------------------------- | -------------------------------------- | -------------------------------------------------------------------------- | ------------------------------------------- |
| **`pickle.dump(obj, file)`** | ➜ Writes **binary data to a file**     | When you want to **save** a serialized object to disk                      | `pickle.dump(data, open('file.pkl', 'wb'))` |
| **`pickle.dumps(obj)`**      | ➜ Returns a **bytes object in memory** | When you want to **store or transmit** serialized data (not write to file) | `serialized = pickle.dumps(data)`           |


## 🧩 4. Using `json` — Text-based, language-independent

### ➤ Serialize to JSON

```
import json

data = {"name": "Anil", "age": 28, "skills": ["Python", "ML"]}

json_str = json.dumps(data)
print(json_str)
# {"name": "Anil", "age": 28, "skills": ["Python", "ML"]}

with open("data.json", "w") as f:
    json.dump(data, f)

```

### ➤ Deserialize JSON

```
loaded = json.loads(json_str)
print(loaded["name"])  # Anil

with open("data.json", "r") as f:
    data_from_file = json.load(f)
```


✅ **Advantages:**

- Human-readable
- Supported in many languages
- Safe for untrusted data

⚠️ **Limitations:**
- Only supports basic types (`dict`, `list`, `str`, `int`, `float`, `bool`, `None`)

|Function|Output type|Used for|Typical usage|
|---|---|---|---|
|**`json.dump(obj, file)`**|➜ Writes JSON **directly to a file**|When you want to **save JSON data to disk**|`json.dump(data, open('data.json', 'w'))`|
|**`json.dumps(obj)`**|➜ Returns JSON **string in memory**|When you want to **store or send** JSON data (not write to file)|`json_string = json.dumps(data)`|

| Serialized using       | Deserialize with  |
| ---------------------- | ----------------- |
| `json.dump(obj, file)` | `json.load(file)` |
| `json.dumps(obj)`      | `json.loads(str)` |

### NOTE:
We can do dumps and write it back to the file 

```
# Step 1: Convert to JSON string
json_str = json.dumps(data)

# Step 2: Manually write the string to a file
with open("data.json", "w") as f:
    f.write(json_str)
```

Output stored in file is same as json.dump:

```
{"name": "Anil", "age": 28, "skills": ["Python", "ML"]}
```


#### ⚙️ But why do we have `json.dump()` then?

Because `json.dump()` just combines both steps:
1. It converts (`dumps`)
2. It writes to a file (`f.write`)

## DUMP VS DUMPS

- it is same for all python
- The pattern of `dump()` vs `dumps()` is **consistent across all Python serialization libraries** like `json`, `pickle`, `yaml`, etc.

|Function|Output type|Writes to|Typical Use|
|---|---|---|---|
|**`dump()`**|Returns nothing (`None`)|**File-like object**|Directly writes serialized data to file|
|**`dumps()`**|Returns serialized data (string or bytes)|**Memory (variable)**|Keeps or sends serialized data|

> Think of **“s”** in `dumps` as **“string”** — it gives you the serialized data _as a string_ (or bytes).

The return type of `dumps()` depends on the library used but dump is used to writing data to disk:

- **`json.dumps()`** → returns a **JSON-formatted string**
- **`pickle.dumps()`** → returns **binary data (bytes)**
- **`yaml.dump()`** → returns a **YAML-formatted string**

## 🧩 5. Handling Custom Objects

If you try to serialize a custom class directly with JSON — you’ll get a `TypeError`.

```
import json

class Person:
    def __init__(self, name, age):
        self.name, self.age = name, age

p = Person("Anil", 28)
# json.dumps(p) -> ❌ TypeError

```

✅ Fix: provide a custom encoder/decoder.

```
def encode_person(obj):
    if isinstance(obj, Person):
        return {"__type__": "Person", "name": obj.name, "age": obj.age}
    raise TypeError(f"Type {obj.__class__.__name__} not serializable")

def decode_person(dct):
    if "__type__" in dct and dct["__type__"] == "Person":
        return Person(dct["name"], dct["age"])
    return dct

json_str = json.dumps(p, default=encode_person)
obj = json.loads(json_str, object_hook=decode_person)
print(obj.name)  # Anil
```


## 🧩 6. `marshal` (⚠️ Use with care)

Used internally by Python for `.pyc` files. Not secure or stable across versions.

```
import marshal

data = {"x": 10}
s = marshal.dumps(data)
print(marshal.loads(s))
```

✅ Faster but unsafe for general serialization.


## 🧩 7. Comparison Table

|Feature|`pickle`|`json`|`marshal`|
|---|---|---|---|
|Format|Binary|Text|Binary|
|Cross-language|❌|✅|❌|
|Safe for untrusted input|❌|✅|❌|
|Supports all Python objects|✅|❌|Limited|
|Human readable|❌|✅|❌|
## 🧮 8. Quick Summary

| Operation           | Module | Code                  |
| ------------------- | ------ | --------------------- |
| Serialize (binary)  | pickle | `pickle.dump(obj, f)` |
| Deserialize         | pickle | `pickle.load(f)`      |
| Serialize (JSON)    | json   | `json.dump(obj, f)`   |
| Deserialize         | json   | `json.load(f)`        |
| Convert to string   | json   | `json.dumps(obj)`     |
| Convert from string | json   | `json.loads(s)`       |
### 💡 Real-world Use Cases

✅ Config or data exchange → `json`  
✅ Save ML model locally → `pickle` (or `joblib`)  
✅ Store custom class data → `json` with encoder/decoder  
✅ Debug-friendly data → `json.dumps(obj, indent=4)`