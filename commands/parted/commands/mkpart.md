# `mkpart`

`mkpart` is the GNU Parted command used to **create a new partition entry in a disk’s partition table**. It defines a partition’s start and end boundaries and optionally encodes a filesystem type in the partition table entry, but it does *not* actually format the partition with a filesystem.

---

## Formal syntax

* **Interactive mode**

  ```
  mkpart [part-type name fs-type] start end
  ```

  * Parted will prompt for missing operands.
  * The `name` operand is mandatory when the disk label is GPT.
  * `fs-type` is required for non‑extended partitions.

* **Non‑interactive (scripted) mode**

  ```
  parted [-s|--script] <device> mkpart [part-type name fs-type] start end
  ```

  * The `-s`/`--script` flag suppresses prompts.
  * All operands must be specified on the command line when scripting.

---

## Parameters

* **`part-type`**

  * Partition classification within the table.
  * Allowed values: `primary`, `extended`, `logical`.
  * Usage constraints: valid only for `msdos` or `dvh` labels.

* **`name`**

  * Partition label string.
  * Required when the disk label is `gpt`.
  * N/A for other labels unless explicitly supported.

* **`fs-type`**

  * Filesystem type to encode into the partition table entry.
  * Examples include: `btrfs`, `ext2`, `ext3`, `ext4`, `fat16`, `fat32`, `hfs`, `linux-swap`, `ntfs`, `xfs`.
  * Required for *data* partitions, optional for extended partitions.

* **`start`**

  * Beginning offset for the new partition relative to disk start.
  * Accepts numbers with units (e.g., `MiB`, `GiB`, `s` for sectors).

* **`end`**

  * Ending offset for the new partition.
  * Same unit and format constraints as `start`.
  * Negative values count backward from disk end (e.g., `-1s`).

---

## Behavioral semantics and side effects

* **Partition table modification**

  * `mkpart` updates the on‑disk partition table immediately; there is no deferred “write” phase as with some older partitioning tools.
  * The tool writes changes directly to the block device when executed.
  * Kernel caches may not reflect changes immediately; a rescan (`partprobe`, `udevadm settle`, or reboot) may be necessary before tools like `lsblk` show new partitions.

* **Filesystem creation**

  * Does *not* create a filesystem; it only defines partition boundaries and a filesystem *type* field in the table.
  * Actual filesystem creation requires external tools (e.g., `mkfs.ext4`).

* **Partition metadata**

  * Parted encodes the specified filesystem type in the partition table’s type byte, aiding OS and bootloader interpretation, but this is semantic only; no data structures are formatted.

* **Mounted filesystems**

  * Creating a partition overlapping mounted device regions may lead to inconsistency and undefined behaviour; ensure target device partitions are unmounted.
  * Parted does not inherently check for mount state before modification.

---

## Error conditions and return values

* **Invalid parameter**

  * Passing an unrecognized `fs-type`, `part-type`, or syntactically invalid offset will cause Parted to emit an error and terminate with a non‑zero exit status in script mode.

* **Overlapping partitions**

  * If `start`/`end` ranges conflict with existing partitions, `mkpart` will prompt (interactive) or fail (scripted), depending on context and flags.

* **Device accessibility**

  * Errors if the device is inaccessible, invalid, or busy (e.g., kernels preventing writes due to I/O issues).

* **Return values**

  * Parted follows Unix convention: `0` for success; non‑zero for errors. Specific non‑zero codes are tool implementation details and not exhaustively documented.

---

## Interactive vs scripted use

* **Interactive (`parted /dev/<device>`)**

  * Prompts for missing operands (`name`, `fs-type`, `start`, `end`).
  * Displays warnings when the command may overwrite existing structures.
  * Execution is “live”: once confirmed, changes are committed immediately.

* **Scripted (`parted -s ... mkpart ...`)**

  * Requires all operands specified ahead of time.
  * Suppresses interactive prompts and warnings.
  * Suitable for automation, provisioning, and scripting contexts.

* **Primary difference**

  * Interactive use involves runtime prompts and user confirmations; scripted use assumes command correctness and does not block for input.

---

## Practical examples

* **Minimal example**

  ```
  sudo parted -s /dev/sdb mkpart primary ext4 1MiB 10GiB
  ```

  * Creates a primary partition that begins at 1MiB and ends at 10GiB, labeling it as `ext4` in the table.
  * Does not format the partition; subsequent `mkfs.ext4 /dev/sdb1` is required.([Linux Command Library][2])

* **Safe real‑world example**

  ```
  sudo parted /dev/sdc
  (parted) print
  (parted) mkpart primary fat32 1MiB 500MiB
  (parted) quit
  ```

  * Interactive creation of a primary FAT32 partition.
  * Use `print` beforehand to confirm free space layout and ensure no overlap with existing partitions.

---

## Safety

* **Destructive potential**

  * Improper `start`/`end` values can overlap existing partitions and corrupt data.
  * Always verify layout (`parted print`, `lsblk`) before issuing `mkpart`.

* **Recommended pre‑checks**

  * Backup any essential data prior to partition table modification.
  * Inspect with:

    * `parted /dev/<device> print`
    * `lsblk -f`
    * `blkid`
  * Confirm no partitions on the target device are mounted.

* **Recovery avenues**

  * If partitions are accidentally destroyed, tools like `parted rescue`, `testdisk`, or `gpart` may recover lost partition boundaries, but success varies and is not guaranteed.

---
