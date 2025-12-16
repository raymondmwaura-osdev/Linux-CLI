# `print`

The `print` command in GNU `parted` is a **query operation** that reads and displays metadata about the partition table of a specified block device. Its primary effect is to present structural information (including partition boundaries, types, sizes, flags, and unallocated space) without modifying on-disk data.

---

## Formal syntax

The `print` command syntax (in both interactive and non-interactive contexts) can be expressed as follows:

```
<print-invocation> ::= "print" [ <print-type> ]
<print-type>       ::= "devices" | "free" | "list" | "all"
```

Where:

* In an interactive parted session: the user issues `print` at the `(parted)` prompt.
* In a non-interactive shell invocation: the command is appended to the invocation of the `parted` program, e.g.,

```
parted /dev/<device> print [print-type]
```

Non-interactive use may be further structured with global options such as `-s`/`--script`.

---

## Parameters

Meaningful parameters and flags relevant to the `print` command are as follows:

* **print-type** → *Optional display modifier.* Determines the scope of the output. Allowed values:

  * `devices`: show all active block devices visible to `parted`.
  * `free`: display unallocated (free) extents on the current block device.
  * `list` / `all`: list partition tables on all active block devices (alias usage).
  * *(none)*: default mode, prints the partition table for the current device.

---

## Behavioral semantics and side effects

`print` is a **read-only reporting operation**:

* It **reads partition table metadata** from the current block device or system view.
* It does **not modify on-disk partition tables or file system content**. As a query operation, its effect is observational only.
* The output is drawn from the on-disk partition table and the kernel’s in-memory representation.
* Because effects are non-mutating, no caching delays or commit semantics are associated *specifically* with `print`.
* Mounted or active file systems are not altered by `print`, though `parted` will read metadata associated with them.

**Note:** Running `print` on an unlabeled or uninitialized disk can report an “unrecognized disk label” or minimal output, indicating no partition table exists.

---

## Error conditions and return values

Typical failure or error conditions include:

* **Device not specified or inaccessible**: if the block device cannot be opened (e.g., due to permissions or non-existence), parted reports an error and returns a non-zero exit status.
* **Unrecognized partition table**: if the device has no recognizable partition table, `print` may show a message indicating “unrecognized disk label” and still output basic geometry.
* **Invalid `print-type` argument**: mistyped modifiers are treated as invalid commands in interactive mode, prompting an error text and return to the prompt.
* In interactive mode, errors are presented textually at the `(parted)` prompt. Non-interactive return codes conform to standard Unix conventions (zero on success, non-zero on error).

---

## Interactive vs scripted use

### Interactive

* Launch `parted` with a block device:

  ```
  sudo parted /dev/sda
  ```

* At the `(parted)` prompt:

  ```
  (parted) print
  ```

* The utility displays partition metadata immediately. There are **no confirmation prompts** for `print` itself.

### Scripted / Non-Interactive

* One can embed the command in a single shell line:

  ```
  sudo parted -s /dev/sda print
  ```

* With `--script`, output is suitable for automation and suppresses interactive prompts. Additional flags such as `--machine` facilitate programmatic parsing.

The principal difference is that interactive use presents a session and can accept multiple commands, whereas scripted use executes and exits, making `print` part of a procedural command sequence.

---

## Practical examples

**Minimal example (interactive):**

```bash
sudo parted /dev/sdb
(parted) print
```

*Expected output:* Partition table for `/dev/sdb` including disk model, size, partition numbers, start/end boundaries, filesystem types, and flags. This confirms what partitions, if any, exist on the device.

**Safe real-world example (scripted):**

```bash
sudo parted -s /dev/sdb print free
```

*Explanation:* Non-interactive invocation that prints free (unpartitioned) space on `/dev/sdb`. This can be used in provisioning scripts to determine available extents before creating partitions.

---

## Safety

While `print` is non-destructive, best practices when working with disk partition tools include:

* **Back up data** prior to partition operations; tools like `print` should be used to inform decisions, not effect change.
* Use `lsblk`, `blkid`, or `smartctl` to corroborate device identity and health before partitioning.
* Avoid operating on unintended disks: confirm device path with `print devices` or similar methods before scripted operations.
* In cases where partition tables are accidentally altered by other parted commands, recovery can involve `parted rescue`, file system-oriented tools (e.g., testdisk), or restoring from backup.

---
