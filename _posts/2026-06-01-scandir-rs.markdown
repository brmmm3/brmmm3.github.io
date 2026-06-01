---

layout: post
title: scandir-rs
tagline: Blazing-fast directory traversal for Python — up to 70× faster than os.walk.
date: 2026-06-01 08:40:00 +0100
categories: posts
-----------------

# scandir-rs: High-Performance Directory Traversal for Python

**File system traversal is often a hidden bottleneck.**

Whether you're indexing files, collecting statistics, searching large directory trees, or building developer tools, performance matters.
That's why I created **scandir-rs**: a Rust-powered Python library designed to be a drop-in replacement for `os.walk()` and `os.scandir()`,
while delivering dramatically better performance and additional functionality.

A new version (2.9.9) is available with following changes compared to the version I've introduced here the last time (2.7.1):

- Add support for Python 3.13 and 3.14
- Add support for MacOS on ARM64
- Add support for Linux on PPC64 and S390
- Add SSE builds (for older CPUs which have no AVX) for Linux and Windows
- Add optional argument `follow_links`.
- Changed `skip_hidden` to false by default

## Why scandir-rs?

Because speed matters...

### 🚀 Significant Performance Improvements

Compared to Python's built-in implementations:

* **Walk** is up to **13× faster on Linux**
* **Walk** is up to **70× faster on Windows**
* **Scandir** is up to **6.5× faster on both Linux and Windows**

When processing millions of files, these speedups can turn minutes into seconds.

### Benchmarks results for running scandir in linux-5.9 folder

<figure>
    <img src="https://raw.githubusercontent.com/brmmm3/scandir-rs/master/scandir/doc/images/linux_scandir_linux-5.9.png" width="48%" alt="scandir benchmarks">
    <figcaption>scandir-rs Walk benchmark on Linux (kernel 5.9)</figcaption>
</figure>

<figure>
    <img src="https://raw.githubusercontent.com/brmmm3/scandir-rs/master/scandir/doc/images/windows_scandir_linux-5.9.png" width="48%" alt="scandir benchmarks">
    <figcaption>scandir-rs Walk benchmark on Windows (kernel 5.9)</figcaption>
</figure>

### 🔍 Richer Metadata

Beyond the standard `os.walk()` and `os.scandir()` APIs, scandir-rs can return:

- Extended file metadata
- Hardlink detection
- Additional file type classification
- Error collection without interrupting traversal

### ⚡ Background Processing

Long-running scans can run asynchronously in the background, allowing your application to process results while scanning is still in progress.

## Installation

```bash
pip install scandir-rs
```

## Usage Examples

### Directory Statistics

Get fast statistics for an entire directory tree:

```python
import scandir_rs as scandir

print(scandir.Count("/usr").collect())
```

### Extended Statistics

Include additional metadata and hardlink detection:

```python
import scandir_rs as scandir

print(
    scandir.Count(
        "/usr",
        return_type=scandir.ReturnType.Ext
    ).collect()
)
```

### Background Scanning

Process results while scanning continues in the background:

```python
import scandir_rs as scandir

counter = scandir.Count("/usr")

with counter:
    while counter.busy:
        results = counter.results()
        # Process intermediate results

# Final results as JSON
results = counter.to_json()
```

## Faster `os.walk()`

A familiar interface with significantly better performance:

```python
import scandir_rs as scandir

for root, dirs, files in scandir.Walk("/usr"):
    # Process files
```

### Extended Walk Information

Retrieve additional file categories and error information:

```python
import scandir_rs as scandir

for root, dirs, files, symlinks, other, errors in scandir.Walk(
    "/usr",
    return_type=scandir.ReturnType.Ext
):
    # Process files
```

On Unix systems, `other` includes special file types such as pipes and devices.

## Faster `os.scandir()`

Collect all entries at once:

```python
import scandir_rs as scandir

entries, errors = scandir.Scandir("/usr").collect()
```

Or iterate lazily:

```python
import scandir_rs as scandir

for entry in scandir.Scandir("/usr"):
    # Process entry
```

### Extended Metadata

Request detailed information for each directory entry:

```python
import scandir_rs as scandir

for entry in scandir.Scandir(
    "/usr",
    return_type=scandir.ReturnType.Ext
):
    # Process entry
```

Entries are returned as `DirEntryExt` objects. Errors are reported as tuples containing:

```python
(relative_path, error_message)
```

allowing scans to continue even when individual files cannot be accessed.

## Benchmark Results

### Walk Performance

| Operation           | Linux            | Windows          |
| ------------------- | ---------------- | ---------------- |
| `Walk` vs `os.walk` | Up to 13× faster | Up to 70× faster |

### Scandir Performance

| Operation                 | Linux             | Windows           |
| ------------------------- | ----------------- | ----------------- |
| `Scandir` vs `os.scandir` | Up to 6.5× faster | Up to 6.5× faster |

For detailed benchmark data and methodology, see the benchmark documentation:

<https://github.com/brmmm3/scandir-rs/blob/master/pyscandir/doc/benchmarks.md>

## Get Started

If your application spends time traversing large directory trees, scandir-rs can provide substantial performance improvements with minimal code changes.

The API is intentionally familiar, making migration from `os.walk()` and `os.scandir()` straightforward
while unlocking additional capabilities and significantly faster execution.

Source code, documentation, and issue tracker:

<https://github.com/brmmm3/scandir-rs>

Licensed under the MIT License.
