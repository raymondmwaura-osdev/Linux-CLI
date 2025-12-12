# `apt install`

## Overview

The `apt install` sub-command serves as the **primary mechanism for installing software packages** on Debian-based systems, including Debian itself, Ubuntu, and their derivatives. Its core function is to **fetch, resolve, and install a specified package or set of packages from enabled APT repositories**, managing all requisite dependencies automatically. In practical workflows, it is typically invoked **after refreshing the package index with `apt update`** to ensure that the most current package versions are available for installation.

When executed, `apt install` evaluates the target package names against the local index, retrieves the necessary `.deb` binary packages from remote mirrors, resolves any unresolved dependencies, and installs the software into the system. It also supports **installing local `.deb` files** when provided with a direct path, distinguishing local files from repository packages based on syntax.

---

## Syntax

```bash
sudo apt install <package>[ ...]
```

* **`<package>`**; One or more target package names or, optionally, a path to a local `.deb` file (e.g., `./mypackage.deb`).

**Essential elements:**

* One or more package identifiers are required; omitted names result in a syntax error.
* Local package installation is enabled by prefixing a path to a `.deb` file (e.g., `./package.deb`).

**Optional considerations (beyond core syntax):**

* Flags such as `-y` (assume “yes” to all prompts) and `--no-install-recommends` (omit suggested packages) modify behavior but do not alter the core structure.

---

## Operational Notes

**Preconditions and Dependencies**

* **Index freshness:** To ensure installation of the latest available version, updating the local package index with `apt update` is strongly recommended prior to installation.
* **Repository access:** Network connectivity and correctly configured repository sources in `/etc/apt/sources.list` and `/etc/apt/sources.list.d/` are required for retrieving remote packages.
* **Privileges:** Administrative (root) privileges are mandatory, typically granted via `sudo`.

**Dependency Resolution**

* APT automatically resolves and retrieves **dependencies** needed by the specified packages, installing them alongside the target packages.
* If a dependency cannot be satisfied due to unavailable or conflicted packages, the installation will fail and report dependency issues.

**Behavioral Characteristics**

* The command will **not reinstall** an already up-to-date package unless explicitly requested with flags such as `--reinstall`.
* Confirmations are usually required for any significant changes (unless overridden by automation flags like `-y`).
* Installing a local `.deb` file with a relative path ensures correct interpretation by APT (`./package.deb`), distinguishing it from a repository package.

**System State After Execution**

* The specified package(s) and any dependencies become installed and active on the system.
* Relevant installation scripts (`preinst`, `postinst`) run during the process to configure software.
* Package metadata and state are recorded in the APT database, allowing future upgrades and removals.

---

## Example

```bash
sudo apt install curl
```

In this representative invocation:

* **`sudo`** ensures necessary privileges.
* **`apt install`** directs APT to fetch and install the `curl` utility.
* Required dependencies are resolved automatically.
* The system prompts for confirmation unless overridden.

This example illustrates the canonical use of the `apt install` sub-command to add software packages from configured repositories.

---
