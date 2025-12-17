# `pacstrap`

## Overview

`pacstrap` is a foundational system-bootstrap utility within the **Arch Linux** ecosystem. It is engineered to **install packages into a specified new root directory**, effectively creating or bootstrapping an Arch Linux system from scratch. This command matters in professional systems operations because it encapsulates a set of complex installation steps (package acquisition, dependency resolution, chroot preparation, and initial configuration) into a structured, repeatable tool designed for both automation and controlled manual deployment. Its strategic relevance lies in rapidly constructing a base system for new installations, virtual machine images, containers, or reproducible build environments. Within typical workflows, `pacstrap` reduces manual error, ensures consistency across installations, and integrates seamlessly with scripting and provisioning pipelines used by systems engineers.

**Core purpose:**  
- Install specified packages into a **target root directory** during system installation.  
- Automate the creation of a minimal working environment without pre-existing system context.

**Common use cases and deployment scenarios:**  
- Fresh Arch Linux installation on bare-metal, VM, or container.  
- Automated OS provisioning via scripts or infrastructure-as-code frameworks.  
- Creating reproducible chroot environments for package building or testing.

**Integration with system components:**  
- Works with `pacman` for package resolution and installation.  
- Uses host configuration (mirrorlist, keys) to bootstrap the target.  
- Prepares the chroot environment for subsequent commands such as `arch-chroot`.

**Strategic value:**  
- **Automation:** Facilitates scripted installation and reproducible build processes.  
- **Operational efficiency:** Bundles multiple setup tasks into a single invocation.  
- **Deployment consistency:** Standardizes the initialization of base systems across environments.

---

## Syntax

```sh
pacstrap [options] <root> [packages...]
```

* `<root>`: Path to the new root directory where packages will be installed.
* `[packages...]`: One or more package names (e.g., `base`, `linux`, `linux-firmware`).
* Options modify installation behavior (e.g., skipping copying host keys, using alternate configs).

---

## Options and Flags

**Configuration and source control**

* `-C <config>`: Use alternate `pacman.conf` for package resolution.
* `-P`: Copy host’s pacman configuration to the target.

**Installation behavior**

* `-i`: Interactive install with user prompt confirmations.
* `--needed`: Do not reinstall packages already up-to-date in target (inherited via options).

**Host/target environment handling**

* `-c`: Use host’s package cache for installations.
* `-G`: Avoid copying host’s pacman keyring to target.
* `-K`: Initialize an empty pacman keyring in target (implies `-G`).
* `-M`: Avoid copying host’s mirrorlist into target.

**Namespace and privilege**

* `-N`: Run in unshare mode (new mount/user namespace) enabling unprivileged installs for advanced use cases.

**Utility**

* `-h`: Display help and exit.

---

## Usage Workflow

1. **Input expectations**

   * A **mounted filesystem** at `<root>` (e.g., `/mnt`) representing the target environment.
   * Root (administrator) privileges, unless using `-N` for unprivileged namespace installs.
   * Internet connection (typical) for remote repository package retrieval.

2. **Command processing**

   * Sets up necessary mountpoints and temporary environment structures.
   * Copies host configuration artifacts (e.g., mirrorlist, keyring) unless overridden.
   * Invokes `pacman` within the context of the specified root to resolve and install packages and dependencies.

3. **Output behavior**

   * Outputs package sync and install progress to **stdout**.
   * Error conditions and warnings are sent to **stderr**.
   * Exit code `0` indicates success; non-zero indicates failure (e.g., missing packages, network issues).

4. **Integration with workflows**

   * Commonly followed by `arch-chroot <root>` for further system configuration.
   * Can be embedded within automation scripts for unattended installations.

---

## Examples

### Default usage

```sh
sudo pacstrap /mnt base linux linux-firmware
```

*Installs core packages to the new root at `/mnt`.*
Expected behavior: Synchronizes package databases and installs required packages.

### Adding Development Tools

```sh
sudo pacstrap /mnt base base-devel linux-lts
```

*Installs base system, development group, and LTS kernel.*

### Using Host Package Cache

```sh
sudo pacstrap -c /mnt base
```

*Uses host package cache to reduce network downloads.*

### Initializing Clean Keyring

```sh
sudo pacstrap -K /mnt base
```

*Initializes an empty keyring on the target, avoiding host key pollution.*

### Interactive Installation

```sh
sudo pacstrap -i /mnt base networkmanager
```

*Prompts the user for confirmation on each package installation.*

---

## Implementation Considerations

* **Filesystem prerequisites:** The target directory must be a valid mount point with appropriate permissions and space.
* **Root permissions:** Typically requires root, though `-N` can enable unprivileged setups.
* **Package resolution:** Dependent on `pacman` configuration and available mirrors.
* **Environmental side effects:** May bind mount temporary structures to satisfy `pacman` execution context.
* **Resource usage:** Network bandwidth and disk I/O are the primary consumables during execution.

---

## Error Handling and Exit Codes

* **Exit code `0`**: Successful completion, base system installed properly.
* **Non-zero codes**:

  * Package retrieval failures (e.g., mirror issues, network errors).
  * Insufficient disk space on target.
  * Missing permissions.
  * Signature or keyring problems requiring pacman keyring updates or refresh (common during installation).
* **Remediation patterns**:

  * Update keyring on installation media (`pacman-key ...`).
  * Confirm mirror availability or modify mirrorlist.
  * Ensure `/mnt` is correctly mounted and has sufficient capacity.
* **Automation patterns**: Scripts should validate mountpoints and exit codes, logging output for diagnostics.

---
