# fastlogging-rs: High-Performance Logging for many different Programming Languages

*Logging is often the hidden bottleneck in your application.*

Every `log.info(...)` call can block your hot path, serialize your threads, and slow down your I/O-bound workloads. That's why I created **fastlogging-rs**: a Rust-powered logging framework that is extremely fast, thread-safe, and available with a similar API in **8 different programming languages**.

My first release, 0.8.1, is available with the following features:

- Initial release of the Rust core (`fastlogging` crate) with bindings for Python, C, C++, Go, Java (FFM and JNI) and C#
- Non-blocking, asynchronous logging — writers run in background threads
- Optional file rotation and compression
- Optional AES encryption for network logging
- Configuration via API or configuration file (JSON, XML, YAML)
- Automatic forwarding of log messages from sub processes to the main process

## Why fastlogging-rs?

Because speed matters...

### 🚀 Significant Performance Improvements

Compared to Python's built-in `logging` module:

- **Writing to a file** is up to **147× faster**
- **Rotating file logging** is up to **207× faster**
- Even compared to **Apache Log4j**, fastlogging-rs is up to **9× faster**

When your application logs millions of messages, these speedups can turn minutes into seconds.

### Benchmarks results for writing to a file

![fastlogging-rs file writer benchmark](https://raw.githubusercontent.com/brmmm3/fastlogging-rs/master/docs/benchmarks/short-noexc-file.svg)

![fastlogging-rs rotating file writer benchmark](https://raw.githubusercontent.com/brmmm3/fastlogging-rs/master/docs/benchmarks/short-noexc-rotate.svg)

### 🌍 One Framework, 8 Languages

`fastlogging-rs` is written in Rust, but it speaks your language. All bindings share a similar API, so you can use the same logging concepts across your whole stack:

- **Rust** — `fastlogging` (native core)
- **Python** — `pyfastlogging` (pyo3, >= 3.10)
- **C** — `cfastlogging` (FFI, cbindgen header)
- **C++** — `cxxfastlogging` (type-safe `cxx` bridge)
- **C++** — `cppfastlogging` (C++17 RAII over the C ABI)
- **Go** — `gofastlogging` (cgo wrapper)
- **Java** — `jfastlogging-ffm` (Foreign Function & Memory API)
- **Java** — `jfastlogging-jni` (Java Native Interface)
- **C#** — `csharpfastlogging` (P/Invoke)

### ⚡ Non-Blocking Architecture

Logging calls are **non-blocking**: each call performs a cheap level check (a single integer comparison, no lock) and hands the message to a channel. A background `LoggingThread` drains that channel and dispatches to each writer's own thread. The speed of your writers never slows down your application — as long as the queue doesn't run full.

### 🔧 Rich Feature Set

- Thread-safe logging calls
- Multiple writers (sinks) per logger: console, file, network, syslog, callback
- Optional file rotation and compression
- Optional AES encryption for network logging
- Configuration via API or configuration file (JSON, XML, YAML)
- Automatic forwarding of log messages from sub processes to the main process

## Installation

**Rust**

```bash
cargo add fastlogging
```

**Python**

```bash
pip install pyfastlogging
```

## Usage Examples

**Rust**

```rust
use fastlogging::{logging_new_default, LoggingError};

fn main() -> Result<(), LoggingError> {
    let mut log = logging_new_default()?;
    log.info("Hello, fastlogging!")?;
    log.shutdown(false)?;
    Ok(())
}
```

**Python**

```python
from fastlogging import Logging

log = Logging()
log.info("Hello, fastlogging!")
log.shutdown(False)
```

**Python with a colored console writer**

```python
from pyfastlogging import TRACE, Logging, ConsoleWriterConfig

logger = Logging(
    TRACE,
    "main",
    [ConsoleWriterConfig(TRACE, True)],
)
logger.trace("Trace Message")
logger.debug("Debug Message")
logger.info("Info Message")
logger.shutdown()
```

## Benchmark Results

**Writing to a file**

- Python logging: **29.37s**
- log4j: **1.48s**
- fastlogging-rs: **0.2s**

**Rotating file logging**

- Python logging: **35.24s**
- log4j: **1.56s**
- fastlogging-rs: **0.17s**

For detailed benchmark data and methodology, see the benchmark documentation:

https://github.com/brmmm3/fastlogging-rs/blob/master/docs/benchmarks/index.html

You can also explore the full benchmark results with interactive charts and tables:

https://brmmm3.github.io/fastlogging-rs/

## Get Started

If your application spends time logging, fastlogging-rs can provide substantial performance improvements with minimal code changes — in whichever language you happen to be writing.

The API is intentionally familiar, making migration from `logging`, `log4j`, or your current framework straightforward while unlocking significantly faster execution.

Source code, documentation, and issue tracker:

https://github.com/brmmm3/fastlogging-rs

Licensed under the MIT or Apache-2.0 License.
