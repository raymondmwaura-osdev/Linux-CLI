# `mklabel`

The `mklabel` command in GNU Parted is used to **create or overwrite the disk label (partition table format) on a block device**. Its primary effect is to initialize the target storage with a new partition table of a specified type, removing all existing partition metadata and making previously existing partitions inaccessible without recovery procedures.

---

## Formal syntax

**Interactive mode**

```
mklabel <label-type>
```

* Where `<label-type>` is a recognized partition table identifier.

If no `<label-type>` is provided, interactive prompts will request it:

```
New disk label type? <label-type>
```

**Non-interactive (scripted) mode**

When invoking `parted` from the shell:

```
parted [-s|--script] <device> mklabel <label-type>
```

* `-s`/`--script` suppresses confirmation prompts.

---

## Parameters

Here’s the `Parameters` section rewritten with bullet points instead of a table, keeping all content intact:

---

## Parameters

* **`<label-type>`**

  * Specifies the new disk label (partition table format).
  * Allowed values: `aix`, `amiga`, `bsd`, `dvh`, `gpt`, `loop`, `mac`, `msdos`, `pc98`, `sun`

* **`-s`, `--script`**

  * Suppresses interactive prompts and warnings, suitable for automation.
  * Boolean switch

* **`<device>`**

  * The target block device to operate on (e.g., `/dev/sda`).
  * Must be a valid block device path

Supported disk labels depend on the Parted build/version and underlying platform.

---

## Behavioral semantics and side effects

* **Semantic effect:** `mklabel` **writes a new partition table to the specified device instance**. This updates the block device’s on-disk metadata immediately, replacing any existing partition table.
* **Immediate persistence:** Changes are committed to disk immediately; Parted does not stage changes in memory like fdisk and then write on a final “write” command.
* **Partition visibility:** Following execution, the existing partitions are removed from the table and become inaccessible to the OS until new partitions are defined. The kernel may cache old table state; tools like `partprobe`, a device re-scan, or reboot may be necessary to see fresh state.
* **Data effects:** Although a new label does not technically overwrite all data sectors, it eradicates critical metadata required for filesystem discovery, effectively rendering data unusable without specialized recovery.
* **Mounted filesystems:** Executing `mklabel` on a device with mounted filesystems may lead to inconsistencies; typical practice is to ensure no partitions on the target device are mounted. Parted does not enforce exclusive access but the operation’s destructive nature makes it unsafe otherwise.

---

## Error conditions and return values

* **Invalid device:** If the specified `<device>` does not exist or is not a block device, Parted will report an error and exit with a non-zero status.
* **Unsupported label:** If `<label-type>` is not recognized, the command fails with an error message indicating acceptable types.
* **Device busy:** If the kernel holds the device active (e.g., mounted partitions), warnings or errors may appear depending on the environment and options used.
* **Exit codes:** Parted generally uses standard Unix exit conventions (`0` for success, non-zero for failure) though specific return values are dependent on implementation and are not exhaustively documented in user manuals.

In **interactive mode**, errors and prompts report directly as text at the `(parted)` prompt; failure may result in aborting the current command request.

---

## Interactive vs scripted use

**Interactive session**

```
sudo parted /dev/sdb
(parted) mklabel
New disk label type? gpt
Warning: The existing disk label… data… lost. Continue? Yes/No? y
```

* Prompts for missing parameters.
* Displays warnings that require user acknowledgment before destructive actions.

**Scripted invocation**

```
sudo parted -s /dev/sdb mklabel gpt
```

* No interactive prompts.
* Suitable for automation or provisioning scripts.
* Fails if ambiguous without user input unless parameters are complete.

The key difference is **prompt suppression and lack of runtime interactivity** under `-s`, enabling integration in scripts and automation pipelines without blocking for confirmation.

---

## Practical examples

### Minimal example

```
sudo parted -s /dev/sdc mklabel msdos
```

**Effect:** This command writes a new **MS-DOS (MBR)** partition table to `/dev/sdc`, overwriting any existing table and removing all partition entries. No interactive prompt appears due to `-s`. After execution, tools like `parted /dev/sdc print` will show an empty partition table with type `msdos`.

### Safe real-world example

```
sudo parted /dev/sdd
(parted) print
(parted) mklabel gpt
```

**Explanation:** First, inspect the existing partition table with `print`. If acceptable to overwrite, issue `mklabel gpt` to apply a **GUID Partition Table (GPT)**. Interactive warnings will surface before writing. Post-execution, run `print` again to confirm the new table and proceed to create partitions.

---

## Safety

**Explicit warnings**

* `mklabel` is inherently **destructive**. It removes existing partition metadata and renders filesystems inaccessible; it should only be used when you intend to reinitialize a disk.

**Best practices**

* **Backup:** Always backup critical data before applying `mklabel`. Recovering partitions after rewriting the table often requires specialized recovery tools and is not guaranteed.

* **Pre-checks:** Use inspection tools:

  ```
  sudo parted /dev/sdX print
  lsblk
  blkid
  ```

  to confirm device identity and contents.

* **Disconnection of mounted partitions:** Ensure no related partitions are mounted; unmount with `umount` where necessary.

**Recovery avenues**

* Parted’s `rescue` command can sometimes recover partition boundaries if called promptly after unintentional label overwrite, but success varies.
* External tools like `testdisk` or `gpart` may assist in reconstructing lost tables, but are not guaranteed.

---
