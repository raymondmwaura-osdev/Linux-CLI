# TOML Syntax

## Overview

TOML (Tom's Obvious, Minimal Language) is a configuration-file format designed with two primary objectives: 1) human readability, and 2) unambiguous mapping to a hash-table (i.e., dictionary) data structure.
Its syntax is inspired by INI-files but is formally specified.

---

## Basic Building Blocks

### Comments

Lines beginning with `#` (hash) are comments and ignored by parsers.

```toml
# This is a comment
key = "value"
```

### Key-Value Pairs

A basic assignment uses the `key = value` pattern. Keys may be bare or quoted (in certain cases). Values are one of several supported types (string, integer, float, boolean, datetime, array, table).

```toml
title = "Example"
enabled = true
count = 42
```

### Keys

* **Bare keys**: unquoted identifiers using letters, digits, underscores, and dashes.
* **Quoted keys**: use double-quotes (or single quotes in limited contexts) when the key contains spaces, special characters, or begins with numeric.
* **Dotted keys**: represent nested tables via `a.b = value`.

---

## Types

### Strings

* Basic string: enclosed in double-quotes (`"..."`) allowing escape sequences.
* Literal string: enclosed in single-quotes (`'...'`), no escape sequences.
* Multi-line string variants: triple-quotes for multi-line basic or literal strings.

### Integers and Floats

Integers: e.g., `42`, `-17`.
Floats: decimal or exponential, underscores permitted for readability (e.g., `1_000.0`, `6.626e-34`).

### Booleans

Simple `true` or `false`.

### Datetime / Date / Time

TOML has native support for ISO-8601 style datetimes, local dates, local times, and offset datetimes.

```toml
ld = 1979-05-27      # local date
lt = 07:32:00        # local time
odt = 1979-05-27T07:32:00Z   # offset datetime
```

### Arrays

Arrays are enclosed in square brackets with comma-separated values. Arrays may be multi-line.

```toml
ports = [8001, 8001, 8002]
hosts = [
  "alpha",
  "omega"
]
```

### Tables

Tables group related key-value pairs. They are denoted by headers in square brackets:

```toml
[owner]
name = "Tom"
dob = 1979-05-27T07:32:00-08:00
```

Nested tables may be indicated via repeated headers or dotted table names:

```toml
[servers.alpha]
ip = "10.0.0.1"
```

---

## Syntax Rules and Nuances

* **Whitespace and indentation**: Indentation is permitted but not semantically required (i.e., whitespace does not change meaning).
* **Key/value ordering**: Order of keys is logically immaterial to semantics in many cases, but for readability consistent ordering is recommended.
* **No trailing commas in simple arrays** (spec depending).
* **Conflicts**: Duplicate table headers or mixing inline table syntax may cause ambiguity.
* **Encoding**: TOML files must be valid UTF-8.

---

## Example Document

```toml
# Sample TOML Document

title = "TOML Example"

[owner]
name = "Tom Preston-Werner"
dob = 1979-05-27T07:32:00-08:00

[database]
server = "192.168.1.1"
ports = [8001, 8001, 8002]
connection_max = 5000
enabled = true

[servers]
  [servers.alpha]
  ip = "10.0.0.1"
  dc = "eqdc10"

  [servers.beta]
  ip = "10.0.0.2"
  dc = "eqdc10"

[clients]
data = [["gamma","delta"], [1,2]]
hosts = [
  "alpha",
  "omega"
]
```

---

## Practical Recommendations for Use

* Group related configuration settings into tables to maintain structure and readability.
* Use arrays when representing lists of homogeneous values.
* Prefer literal or basic strings depending on whether you need escapes or multi-line support.
* Use dotted key notation thoughtfully for nested configuration.
* Maintain comments to document intent of settings.
* Validate your TOML against the official spec (v1.0.0) to ensure cross-language compatibility.

---
