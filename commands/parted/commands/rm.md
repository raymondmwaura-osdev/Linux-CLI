# `rm`

`rm` in GNU Parted is a **partition deletion command** that removes a specified partition entry from the on‑disk partition table. It does *not* erase partition contents; rather, it removes the partition’s metadata entry, making its space available for reuse and effectively invalidating the filesystem until recovery actions are taken. 

---

## Formal syntax

**Interactive mode**

```
rm <number>
```

* Parted prompts for the partition number if not provided.
* `<number>` denotes the partition index as displayed by the `print` command.

**Non‑interactive (scripted) mode**

```
parted [-s|--script] <device> rm <number>
```

* `-s`/`--script` suppresses interactive prompts and warnings.
* All operands must be provided to avoid interactive prompting.

---

## Parameters

* **`<number>`**

  * *Partition identifier → The index of the partition to delete.*
  * *Allowed values:* Positive integers corresponding to entries in the current partition table (e.g., `1`, `2`, … as shown by `print`).

---

## Behavioral semantics and side effects

* **On‑disk metadata modification**

  * `rm` **alters the partition table immediately** by removing the specified entry; this change is written directly to the device without a separate “write” step.

* **Data accessibility**

  * Although the data content on the deleted partition’s sectors remains, the partition is removed from the table and becomes inaccessible through normal filesystem paths. It is effectively lost unless recovered with specialized tools.

* **msdos label renumbering**

  * On **MS‑DOS partition labels**, deleting a **logical partition** renumbers subsequent logicals: e.g., removing partition `6` renumbers original `7`, `8`, `9` to `6`, `7`, `8`. This may require updating system references like `/etc/fstab`.

* **Cache and kernel state**

  * The kernel’s block device cache may not reflect the deletion until a partition table rescan is triggered (e.g., via `partprobe`, `udevadm settle`, or reboot).

* **Mounted partitions**

  * Removing a partition that is *mounted* or in use can cause inconsistencies; standard practice is to unmount and deactivate swap before issuing `rm`.

---

## Error conditions and return values

* **Invalid partition number**

  * If `<number>` does not correspond to an existing partition, Parted reports an error and, in script mode, returns a non‑zero exit status.

* **Device issues**

  * If the target device is inaccessible, busy, or unrecognized, Parted will fail and produce an error diagnostic.

* **Status reporting**

  * Parted in non‑interactive mode returns `0` on success or a non‑zero code on failure; precise codes are implementation details and not exhaustively documented.

* **Interactive error messages**

  * In interactive use, errors appear as textual messages at the `(parted)` prompt and do not yield explicit numeric exit codes until the shell invocation completes.

---

## Interactive vs scripted use

* **Interactive session**

  ```
  sudo parted /dev/sdb
  (parted) print
  (parted) rm 2
  Warning: This will remove partition 2. Continue? Yes/No?
  ```

  * Prompts for confirmation and handles incomplete invocations by asking for missing data.

* **Scripted invocation**

  ```
  sudo parted -s /dev/sdb rm 2
  ```

  * Executes without prompts; suitable for automation.
  * If arguments are incomplete (e.g., missing `<number>`), the command fails instead of prompting.

* **Difference summary**

  * Interactive mode includes confirmations and error context; scripted mode assumes correctness of provided operands and suppresses confirmations for use in automated workflows.

---

## Practical examples

* **Minimal example**

  ```
  sudo parted -s /dev/sdc rm 1
  ```

  * Removes the first partition from `/dev/sdc`. No prompts appear due to `-s`. Post‑execution, the partition table no longer includes entry `1`. Any dependent filesystems must be unmounted beforehand.

* **Safe real‑world example**

  ```
  sudo parted /dev/sdd
  (parted) print
  (parted) rm 3
  (parted) print
  (parted) quit
  ```

  * Inspect the table with `print` first, then remove partition `3`. The subsequent `print` should show that entry `3` is gone. Exit with `quit`.

---

## Safety

**Explicit warnings**

* `rm` is inherently **destructive** to the partition map: it deletes metadata irreversibly unless recovery tools are used.

**Best practices**

* **Back up partition tables and data** before deletion.
* **Unmount partitions** and disable swap (`swapoff`) before removal.
* Use `parted print`, `lsblk`, and `blkid` to confirm correct partition index before issuing `rm`.

**Recovery avenues**

* **Parted rescue:** Use the `rescue` command with approximate start/end parameters to attempt recovery of a deleted partition.
* **External tools:** Utilities like `gpart` or `testdisk` may assist in reconstructing lost partition boundaries, though success is not guaranteed.

---
