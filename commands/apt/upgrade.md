# `apt upgrade`

## Overview

The `apt upgrade` sub‑command is an **operational upgrade mechanism within the Debian/Ubuntu APT package management framework** that installs available upstream package updates for all currently installed packages on the system. This sub‑command uses previously refreshed package index data (typically obtained by `apt update`) to determine which installed packages have newer versions available and attempts to apply these updates in a **conservative, non‑destructive manner**.

In the standard maintenance workflow for Debian‑based systems, `apt upgrade` is typically invoked **after** a repository metadata refresh (`apt update`). Its role is to **safely bring all installed packages to the most recent versions permitted by the configured repositories, without removing existing packages or installing new ones to satisfy dependencies**.

The command’s default behavior avoids major changes: if an update would require adding new packages or removing currently installed ones (e.g., to satisfy a new dependency), the upgrade for that specific package is **held back** and not applied. This behavior supports stability in production and enterprise environments where uncontrolled package removals are undesirable.

---

## Syntax

```bash
sudo apt upgrade
```

**Essential elements:**

* No arguments are required; the command applies to the full set of installed packages by default.
* A successful run presupposes that the local package index has already been synchronized with remote repositories (via `apt update`).

**Optional elements (beyond core syntax):**

* Flags such as `-y` (assume “yes” to prompts) can streamline unattended execution, particularly in scripted workflows.
* Other switches (e.g., `--with‑holdpkg`, `--no‑install‑recommends`) can influence behavior but are beyond the scope of the core sub‑command structure.

---

## Operational Notes

**Preconditions and Dependencies**

* **Package index freshness**: The command depends on up‑to‑date repository metadata; running `apt update` immediately before `apt upgrade` is considered best practice.
* **Network connectivity**: Access to the repositories defined in `/etc/apt/sources.list` (and any `.list` files in `/etc/apt/sources.list.d/`) must be available to download updated packages.
* **Privileges**: Root/user with appropriate permissions is required for package installation operations.

**Behavioral Characteristics**

* **Conservative upgrade policy**: Packages requiring removal of existing software or installation of new dependencies are **not upgraded** and are typically reported as “kept back”.
* **No removal of installed packages**: Under default invocation, `apt upgrade` never removes currently installed packages, preserving the existing system footprint.
* **Dependency handling**: It only installs **needed dependencies** that already exist or are required by upgrades that do not otherwise violate the non‑destructive rule.

**Sequencing and Workflow Integration**

* **Run after `apt update`**: Because it uses refreshed index data, it should follow an index refresh.
* **Follow‑on to upgrades**: In environments where more complex dependency changes are needed (e.g., distribution upgrades), administrators may use `apt full‑upgrade` or similar tools after reviewing the packages held back.

**User/System Feedback**

* The command presents:

  * a list of packages that will be upgraded,
  * any held back packages,
  * summary statistics (download size, disk space changes),
  * a prompt for user confirmation (unless overridden by options such as `‑y`).
* After successful execution, installed packages are at their latest permissible versions without impacting system stability through removals.

---
