
Default level is warning not info.
## The Big Picture

Python’s `logging` system has 3 main parts:

```
Logger  →  Handler  →  Formatter
```

- **Logger** — where you call `logger.info()`, `logger.error()`, etc.
- **Handler** — decides _where_ the log goes (console, file, HTTP, etc.).
- **Formatter** — decides _how_ it looks (plain text, JSON, custom format).

Each log message flows through those three.

## Handler

A **handler** in Python’s `logging` module is responsible for **deciding where your log messages go** — for example, to the console, a file, an email, or even over a network.

In simple terms:

> A _handler_ takes the log records created by your logger and sends them to a specific destination.

You can attach one or more handlers to the same logger to send logs to multiple places at once (e.g., both console and file).

```
import logging

logger = logging.getLogger(__name__)

# Send logs to the console
console_handler = logging.StreamHandler()

# Send logs to a file
file_handler = logging.FileHandler("app.log")

logger.addHandler(console_handler)
logger.addHandler(file_handler)

```

####  Common Handlers and Their Destinations

|**Handler**|**Destination**|**Description**|
|---|---|---|
|`StreamHandler()`|Console (`stdout` / `stderr`)|Sends logs to the terminal or any stream (default is the console). Useful for seeing logs while your program runs.|
|`FileHandler('file.log')`|File|Writes log messages to a specified file. Good for saving logs for later review.|
|`RotatingFileHandler()`|File (with rotation)|Writes logs to a file but automatically creates a new file when the current one reaches a certain size. Prevents log files from growing too large.|
|`TimedRotatingFileHandler()`|File (time-based rotation)|Creates a new log file at regular intervals (e.g., every day or hour). Ideal for daily or hourly log separation.|
|`SMTPHandler()`|Email|Sends log records via email. Often used to alert administrators about critical errors.|
|`SocketHandler()`|Network socket|Sends logs over a TCP/IP socket to a remote server for centralized logging.|
|`QueueHandler()`|Queue|Places log messages into a queue for processing by another thread or process. Helps prevent logging from blocking your main application.|
## Formatter

A formatter controls _how the log line looks_ —  
what fields appear, and in what format.

```
formatter = logging.Formatter("%(asctime)s - %(levelname)s - %(message)s")

Output:
2025-10-14 13:22:01 - INFO - User logged in

```

We can also format our logs into JSON using python-json-logger module

```

formatter = jsonlogger.JsonFormatter("%(asctime)s %(levelname)s %(message)s")

Output:

{
  "asctime": "2025-10-14T13:22:01",
  "levelname": "INFO",
  "message": "User logged in"
}

```

we add formatter to the handler
## Get logger

You **almost never** create a `Logger()` manually.
The function `logging.getLogger(name)` handles two things for you:
1. **Creates** a new logger if it doesn’t exist.
2. **Reuses** it if it already exists (so logs stay consistent).

That means you can safely call `getLogger()` in multiple files,  
and you’ll get the _same logger object_ if you use the same name.

#### **Logger Hierarchy**

- Always one **root logger** at the top (created automatically).
- All loggers form a **tree hierarchy**:
  
  ```
	root
	 └── app
	      ├── app.api
	      └── app.db

  ```
- Child loggers **propagate** messages up to the root if they have no handler.



## Combining all

#### Step 1️⃣ — Create a Handler

A **handler** decides _where_ logs go (console, file, etc.)

```
import logging
handler = logging.StreamHandler()      # sends logs to console (stdout)
```

#### Step 2️⃣ — Create and Attach a Formatter

A **formatter** decides _how_ logs look.

```
formatter = logging.Formatter("%(asctime)s - %(levelname)s - %(message)s")
handler.setFormatter(formatter)
```

🧠 For JSON logs (structured logs):

```
from pythonjsonlogger import jsonlogger
formatter = jsonlogger.JsonFormatter("%(asctime)s %(levelname)s %(message)s")
handler.setFormatter(formatter)
```

#### Step 3️⃣ — Get (or create) a Logger

A **logger** is the interface you use to actually log messages.

```
logger = logging.getLogger("my_app")
# practically we do logger=logging.getLogger(__name__) this name is the file name where this is code is written

logger.setLevel(logging.INFO)
```

Add the handler you just created:
```
logger.addHandler(handler)
```

Thats it you can use the logger

logger.info("Hi)

## Order

| Step | Component        | Purpose                                  |
| ---- | ---------------- | ---------------------------------------- |
| 1    | **Handler**      | Where logs go (console, file, etc.)      |
| 2    | **Formatter**    | How logs look (text or JSON)             |
| 3    | **Logger**       | The interface you call (`logger.info()`) |
| 4    | **Add Handler**  | Attach handler(s) to logger              |
| 5    | **Log messages** | Write logs with desired level            |


