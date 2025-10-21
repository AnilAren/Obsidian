
 - [[Data Type Complexity]]
 - [[Module Package Library]]
 - [[Iterators]]
 - [[Generators]]
 - [[Generator Vs Iterator]]
 - [[Decorators]]
 - [[Exception Handling]]
 - [[Multithreading]]
 - [[Multiprocessing]]
 - [[Async]]
 - [[Multithreading vs Multiprocessing vs Async]]
 - [[Async vs Asyncio]]
 - [[Logger]]
 - [[ContextVar]]
 - [[venv vs poetry]]
 - [[pip vs poetry vs uv]]
 - [[OOPs , Dunder Methods]]
 - [[Design Patterns]]
 - [[Memory and Performance]]
 - [[Functional Programming]]
 - [[File and I/O Handling]]
 - [[Serialization]]
 - [[Packaging & Imports]]
 -  [[PEP 8]]

| Category                                                       | Topic                                                                                                 | Why It Matters                                                                                      |
| -------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------- |
| **OOP & Design**                                               | Classes, `__init__`, inheritance, composition, dunder methods (`__str__`, `__repr__`, `__eq__`, etc.) | Most Python interviews expect OOP understanding — especially system design or API design questions. |
| **Memory & Performance**                                       | Shallow vs Deep Copy, Mutable vs Immutable types, Garbage Collection (`gc`), reference counting       | Used to test your understanding of Python internals.                                                |
| **Data Structures**                                            | `list`, `dict`, `set`, `tuple` internals, comprehension syntax, sorting key functions                 | Simple but often the first filter round.                                                            |
| **Functional Programming**                                     | `lambda`, `map`, `filter`, `reduce`, `functools.partial`, `functools.lru_cache`                       | Adds functional flavor — important in concise pipeline-style code.                                  |
| **Typing & Dataclasses**                                       | `typing` module (Type Hints, Generics), `@dataclass`                                                  | Increasingly expected in modern Python (especially backend + AI).                                   |
| **File & I/O Handling**                                        | `with open()`, file reading/writing modes, JSON/CSV/Parquet usage                                     | Common in data engineering / backend tasks.                                                         |
| **Context Managers**                                           | `with` statement, `__enter__` / `__exit__`                                                            | Core concept that complements `ContextVar`.                                                         |
| **Modules for Concurrency**                                    | `concurrent.futures` (`ThreadPoolExecutor`, `ProcessPoolExecutor`)                                    | Clean abstraction often asked about.                                                                |
| **Standard Libraries Awareness**                               | `os`, `sys`, `pathlib`, `datetime`, `collections`, `itertools`, `functools`                           | Interviewers check how familiar you are with Python’s built-ins.                                    |
| **Testing**                                                    | `pytest` basics, mocking, fixtures                                                                    | Almost always part of backend engineering interviews.                                               |
| **Serialization**                                              | `pickle`, JSON encoding/decoding, Pydantic models                                                     | Especially relevant in ML or API work.                                                              |
| **Packaging & Imports**                                        | Relative vs absolute imports, `__init__.py` purpose                                                   | Crucial when working on multi-package repos.                                                        |
| **Python Internals**                                           | GIL (Global Interpreter Lock) — how it affects threading                                              | Every experienced Python dev should know this.                                                      |
| Topic                                                          | Why Useful                                                                                            |                                                                                                     |
| `asyncio.gather`, `asyncio.run` — advanced async orchestration | For async-heavy services.                                                                             |                                                                                                     |
| Caching (`functools.lru_cache`, Redis)                         | For scalable API work.                                                                                |                                                                                                     |
| Profiling (`timeit`, `cProfile`)                               | Helps in optimization discussions.                                                                    |                                                                                                     |
| Memory profiling (`tracemalloc`)                               | For deep debugging roles.                                                                             |                                                                                                     |
| Metaclasses                                                    | Rare but signals advanced knowledge.                                                                  |                                                                                                     |
| Dependency Injection in Python                                 | Used in large-scale backends.                                                                         |                                                                                                     |
| Reflection / Introspection (`getattr`, `setattr`, `dir`)       | Useful for dynamic framework-style questions.                                                         |                                                                                                     |
