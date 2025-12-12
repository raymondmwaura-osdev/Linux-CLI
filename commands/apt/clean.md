# `apt clean`

## Overview

The `apt clean` sub-command is a **package cache management utility** in the Debian/Ubuntu Advanced Package Tool (APT) suite that **completely removes all locally cached package archive files**. These cached files consist of `.deb` binaries that were downloaded from remote repositories during prior `apt install`, `apt upgrade`, or `apt full-upgrade` operations. Over time, this cache (located primarily under `/var/cache/apt/archives/`) can consume significant disk space; `apt clean` addresses this by purging the cache directory.

In the broader APT maintenance workflow, `apt clean` is typically employed **after updates and installations** when an administrator wants to **reclaim disk space or reset the local package cache**. It does **not affect installed software or the package index itself**; rather, it targets only the stored download artifacts, enabling a cleaner system state particularly in environments with limited storage.

---

## Syntax

```bash
sudo apt clean
```

**Essential elements:**

* There are **no positional arguments**; the command operates on the entire APT cache by default.
* The command does **not require any additional input** beyond privileges and invocation.

**Optional considerations (beyond core syntax):**

* Some environments support a `--dry-run` flag (e.g., `sudo apt clean --dry-run`) that displays what would be deleted without performing the action, useful for auditing before execution.

---

## Operational Notes

**Preconditions and Dependencies**

* **Administrative privileges** are mandatory because the cache directories (`/var/cache/apt/archives/` and subdirectories) are owned by the system and not writable by unprivileged users.
* **No dependency on network state**: `apt clean` operates entirely on local files and does not contact remote repositories.

**Behavioral Characteristics**

* When executed, `apt clean` **removes all downloaded `.deb` archive files** from the local package cache, including files from `/var/cache/apt/archives/` and often its `partial/` subdirectory.
* It **does not remove the package index** (the metadata about available packages), nor does it uninstall installed packages.
* After cleaning, if the same packages need to be reinstalled or inspected, APT will **redownload them from remote repositories**, since they no longer exist in the local cache.

**Sequencing and Workflow Considerations**

* `apt clean` is frequently used **after a series of package installs, upgrades, or full system updates** to reclaim space used by downloaded archives.
* It can be combined in maintenance routines with `apt autoremove` (for cleaning unused dependencies) and `apt autoclean` (for more selective cache pruning) to optimize system cleanliness without affecting installed software.

**System Feedback and State Changes**

* Typically, `apt clean` **does not produce verbose output** when successful; its absence of output generally indicates that the cache has been cleared.
* The system state shifts in terms of **disk usage**: significant space may be freed depending on the cache size.
* The local package index and installed package database remain **unchanged**.

---
