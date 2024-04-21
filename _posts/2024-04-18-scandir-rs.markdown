---
layout: post
title:  scandir-rs
tagline: A faster alternative to os.walk and os.scandir. It also comes with some additional features.
date:   2024-04-21 11:40:00 +0100
categories: posts
---

Because speed matters...

I've released a new version of [scandir_rs](https://pypi.org/project/scandir-rs/) with better performance and some small improvements.

The API is a bit different and provides more features. But it should be easy to replace `os.walk` and `os.scandir` with `scandir-rs`.

## Usage examples

Get statistics of a directory:

```python
import scandir_rs as scandir

print(scandir.Count("/usr").collect())
```

Get extended statistics (also identify hardlinks) of a directory:

```python
import scandir_rs as scandir

print(scandir.Count("/usr", return_Type=scandir.ReturnType.Ext).collect())
```

The same, but asynchronously in background using a class instance and a context manager:

```python
import scandir_rs as scandir

C = scandir.Count("/usr")
with C:
    while C.busy:
        results = C.results()
        # Do something
# Get final results as JSON
results = C.to_json()
```

`os.walk()` examples:

```python
import scandir_rs as scandir

for root, dirs, files in scandir.Walk("/usr"):
    # Do something
```

Or get extended file types (On Unix `other` contains pipes and devices):

```python
import scandir_rs as scandir

for root, dirs, files, symlinks, other, errors in scandir.Walk("/usr", return_type=ReturnType.Ext):
    # Do something
```

`os.scandir()` example:

```python
import scandir_rs as scandir

entries, errors = scandir.Scandir("/usr").collect()
```

Or iterate through entries (`entry` is of type `DirEntry` or in case of an error a tuple with 2 strings. First string is the relative path and the second string the error message):

```python
import scandir_rs as scandir

for entry in scandir.Scandir("/usr"):
    # Do something
```

Or with extended metadata (`entry` is of type `DirEntryExt` or in case of an error a tuple with 2 strings):

```python
import scandir_rs as scandir

for entry in scandir.Scandir("/usr", return_type=ReturnType.Ext):
    # Do something
```

## Benchmarks

On Linux `Walk` is up to 13 times faster than `os.walk` and on Windows up to 70 times faster.  
On Linux and on Windows `Scandir` is up to 6.5 times faster than `os.scandir`.

For detailed benchmarks see [here](https://github.com/brmmm3/scandir-rs/blob/master/pyscandir/doc/benchmarks.md)

Check out the [scandir-rs][scandir-rs] module on github, licensed under the MIT license.

[scandir-rs]: https://github.com/brmmm3/scandir-rs
