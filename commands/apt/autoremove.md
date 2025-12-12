# `apt autoremove`

## Overview

The `apt autoremove` sub-command is a **system maintenance utility** in Debian-based distributions (such as Debian and Ubuntu) that **automatically removes packages that were installed as dependencies but are no longer required by any installed package**. These orphaned or “automatically installed” packages frequently arise when a previously installed package is removed or upgraded in a way that eliminates its dependency on certain libraries or ancillary packages. By removing such packages, `apt autoremove` helps **declutter a system, free disk space, and reduce the surface area for potential security issues**.

The command fits into the broader `apt` workflow as a **post-removal or post-upgrade cleanup step**: after you remove specific software with `apt remove` or perform upgrades that change dependencies, `apt autoremove` identifies and purges all remaining dependencies that no longer have a supporting parent package.

---

## Syntax

```bash
sudo apt autoremove
```

**Essential elements:**

* This invocation requires no positional arguments; it operates on all eligible packages in the system’s package database.
* The command uses internal metadata that tracks which packages were installed automatically versus manually.

**Optional considerations (beyond core syntax):**

* The `--purge` flag (`apt autoremove --purge`) can be appended to also remove configuration files of automatically removed packages, although caution is required as this broadens the impact of the cleanup.

---

## Operational Notes

**Preconditions and Dependencies**

* **Administrative Privileges**: `sudo` or equivalent root permissions are required because package removals affect system-wide state.
* **Accurate Package Metadata**: The command relies on up-to-date status information managed by APT; while `apt update` is not strictly required before running `autoremove`, recent metadata improves the accuracy of what is considered unnecessary.

**Automatic vs. Manual Package Designation**

* APT records whether packages were installed manually (explicitly by the administrator) or automatically as dependencies. Only the latter are candidate targets for `autoremove`.
* Packages explicitly marked as manual will not be removed automatically; even if no other package depends on them. Administrators can influence these flags using `apt-mark auto` or `apt-mark manual`.

**Behavioral Characteristics**

* The tool analyzes the current dependency graph and identifies those packages that were **installed automatically to satisfy dependencies** but are no longer required by any manually installed package.
* It produces a **list of candidate packages** and typically asks for confirmation before removing them.
* Orphaned packages from prior upgrades (e.g., old kernel modules or libraries replaced by newer versions) are also potential candidates. However, some packages (e.g., essential base system files or those protected by policy) may not be auto-removed unless explicitly marked.

**Impact on System State**

* After execution, all listed orphaned packages are uninstalled.
* Disk usage is reduced as redundant libraries and tools are cleared from `/var/lib/apt/lists/` and related directories.
* If the `--purge` flag is used, residual configuration files for removed packages are also deleted, further cleaning the system.

---
