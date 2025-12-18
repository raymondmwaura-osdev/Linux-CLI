# `enable`

## Overview

The `enable` sub-command of `systemctl` is used to configure a **systemd unit** (most often a service) to **start automatically during system boot** or another specified activation scenario. It is a declarative mechanism that establishes persistence of a unit’s autostart behavior by creating or modifying **symbolic links** in the appropriate `systemd` configuration directories that point from a target’s `.wants` or `.requires` folder to the unit’s definition file.

In typical system administration workflows, `systemctl enable` is employed **after installation or configuration of a service** to ensure it participates in the host’s runtime lifecycle without manual triggers on every startup. It operates orthogonally to commands that affect the **immediate runtime state** (such as `start`), instead focusing on the **next and subsequent boots**.

The default behavior when enabling a unit is to create one or more persistent symbolic links; it **does not start the unit immediately** unless specific switches are provided (e.g., `--now`).

---

## Syntax

```text
systemctl enable [OPTIONS...] UNIT...
```

* **UNIT**
  One or more systemd unit names (e.g., `nginx.service`, `docker.service`). These refer to the unit configuration files that the systemd manager understands and manages.

* **OPTIONS**
  Optional modifiers that tailor the domain (system vs. user), session persistence, and immediate action behavior.

The essential structural elements are:

* The `systemctl` command.
* The `enable` sub-command.
* One or more **unit identifiers**.

Optional elements (such as flags modifying context or timing) are not required for the command to affect default persistent behavior.

---

## Switches

- **`--now`** (long form only; optional)  
  Enables the specified unit and immediately starts it in the current runtime session. By default, `enable` affects only future boots; this switch bridges persistence and immediate activation in a single operation.

- **`--system`** (long form only; optional; default behavior)  
  Operates on the system-wide service manager. Units are enabled for all users and evaluated during system boot. This is the implicit context if no domain switch is provided.

- **`--user`** (long form only; optional)  
  Executes the operation in the per-user service manager context. The unit is enabled for the invoking user and is activated as part of that user’s login or session lifecycle rather than system startup.

- **`--runtime`** (long form only; optional)  
  Enables the unit only for the current boot session. No persistent symbolic links are created, and the configuration is discarded on reboot.

- **`--global`** (long form only; optional; valid only with `--user`)  
  Enables the unit for all users’ future login sessions. This does not affect the system manager and requires appropriate permissions and configuration support.

- **`--force`, `-f`** (long and short forms; optional)  
  Forces the creation or correction of symbolic links, even if existing links are present or point elsewhere. This is typically used in reconfiguration or remediation scenarios.

- **`--preset`** (long form only; optional)  
  Applies enablement or disablement based on predefined preset policies rather than explicit user intent. The outcome is determined by system-defined preset rules.

- **`--root=PATH`** (long form only; optional)  
  Targets an alternative root directory instead of the running system. This is commonly used for offline system images, chroot environments, or provisioning workflows.

**Constraints and interactions:**  
- `--system`, `--user`, and `--global` define mutually exclusive operational domains; only one may be specified per invocation.  
- `--now` may be combined with any persistent enablement mode to synchronize runtime state with boot-time configuration.

---

## Operational Notes

**Preconditions and Dependencies**

* The unit file being enabled must exist and be recognized by `systemd` (i.e., installed in a standard search path such as `/usr/lib/systemd/system` or `/etc/systemd/system`).
* Enabling a unit that **lacks an `[Install]` section with `WantedBy=` or equivalent directives** may fail with an explicit message explaining the inability to create necessary links.

**Ordering and Sequencing**

* If modifications have been made to unit files (such as after creation or editing), a `systemctl daemon-reload` may be necessary prior to enabling, to ensure the manager reads the latest definitions.
* `enable` does **not affect the current runtime state** unless combined with switches like `--now`. For immediate activation without reboot, pair with `start` or use `--now`.

**Post-Execution System State**

* On success, symbolic links will appear in **target directories** that determine activation at boot (e.g., `/etc/systemd/system/multi-user.target.wants/`).
* Users will typically observe **confirmation output** indicating link creation, though no service output appears unless the service is also started.

---

## Example

```bash
sudo systemctl enable nginx.service
```

In this representative invocation:

* `nginx.service` is configured to start automatically on subsequent system boots.
* A persistent symbolic link is created from the appropriate `.wants` directory to the unit file.
* The command does not start `nginx` in the current session; only future boots are affected.

---
