# `apt remove`

## Overview

The `apt remove` sub-command is a **package removal operation** within the Debian/Ubuntu APT ecosystem that **uninstalls specified packages** from a system while retaining their configuration files. Its primary function is to delete the executable binaries and associated installed files for a package without destroying local configuration data, enabling a reinstallation later without loss of customized settings. This behavior contrasts with the `purge` variant, which removes configuration files as well.

In the broader lifecycle of package management, `apt remove` is typically invoked when a package is no longer required or desired on a system but the administrator wants to **preserve stateful configuration** for potential future reuse or reference. It fits within workflows that include **searching for packages**, **inspecting dependencies**, and **cleaning up unused packages** (e.g., with `apt autoremove`) after removal.

The effect of executing `apt remove` is that the specified packages are uninstalled and their files are removed from the system, while **configuration files are left in place on disk**. This allows a subsequent reinstall to reuse existing configurations, reducing re-configuration effort.

---

## Syntax

```bash
sudo apt remove <package>[ ...]
```

* `<package>`; One or more names of packages to remove.

**Essential elements:**

* One or more **package names** are required for execution; omitting them results in an error.
* The package names must match installed packages known to the APT database.

**Optional elements (beyond core syntax):**

* Flags such as `-y` (assume “yes” to prompts) can automate confirmation.
* Multiple package names can be supplied in a single invocation to remove several packages atomically.

---

## Operational Notes

**Preconditions and Dependencies**

* **Administrative privileges** (e.g., using `sudo`) are required to remove packages because the operation modifies system state.
* **Repository and package index freshness** are less critical than for installation but are still recommended to ensure that package metadata is accurate. Typically this is achieved by running `sudo apt update` beforehand.

**Dependency Handling**

* When a package is removed, APT **resolves dependencies** and may also remove other installed packages **that depend on the target package** if they cannot remain functional without it. This is a consequence of dependency resolution logic.
* Orphaned dependencies (packages that were installed solely to satisfy dependencies of the removed packages and are no longer needed) are **not automatically removed** by `apt remove` itself; a subsequent `apt autoremove` can be used to clean them up.

**Configuration Files**

* By default, `apt remove` **retains local configuration files** associated with the removed package. These files often reside under `/etc` or user directories and are preserved for potential reuse.
* If complete removal (including configuration files) is desired, `apt purge` should be used instead.

**System Feedback and Changes**

* During execution, the system displays the list of packages to be removed along with any additional packages affected due to dependency relationships.
* The user is typically prompted to confirm the action unless overridden by automation flags.
* Once completed, the specified packages are no longer installed, and disk space previously used by their binaries is freed, though configuration remnants remain.

---

## Example

```bash
sudo apt remove firefox
```

This example demonstrates the canonical use of the `apt remove` sub-command:

* `sudo` grants the necessary privileges.
* `apt remove firefox` instructs APT to uninstall the `firefox` package from the system.
* Configuration files for Firefox are retained on the system for potential future reuse.

This invocation is representative of routine package removal operations.

---
