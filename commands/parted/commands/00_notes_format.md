# `<command>` e.g. `print`

Concise academic description of the command’s intent and primary effect.

---

## Formal syntax

Exact syntax variants (interactive vs non-interactive), required operands and optional operands, and canonical order.

---

## Parameters

Enumerate and briefly define every meaningful parameter and flag relevant to the command (name → short description → allowed values / units).

---

## Behavioral semantics and side effects

What the command changes or reads; durability; whether it modifies on-disk metadata immediately; whether it can render data unusable; how it interacts with mounted filesystems or caches.

---

## Error conditions and return values

Typical failure cases, exit codes (if documented), and how errors are reported in interactive mode.

---

## Interactive vs scripted use

How the command behaves in an interactive `parted` session and how to call it non-interactively from a shell. Note differences (prompts, confirmations, environment).

---

## Practical examples

At least two concrete examples: a minimal example and a safe real-world example. Each example must include command line exactly as run and an explanation of the expected output or effect.

---

## Safety

Explicit warnings, best practices to avoid data loss, recommended pre-checks (e.g., backups, `parted -l`, `lsblk`, `smartctl`), and recovery avenues (e.g., `rescue` command or external tools).

---
