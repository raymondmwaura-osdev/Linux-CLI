# `status`

## Overview

The `status` sub‑command of timedatectl displays the current system clock and RTC (Real Time Clock) settings.

It is commonly used when an administrator needs to quickly verify the system’s notion of time, timezone configuration, whether NTP (network time synchronization) is enabled or active, and how the hardware clock (RTC) is configured. It fits into the broader `timedatectl` workflow as a read‑only diagnostic or status check before or after making changes (e.g. via `set-time`, `set-timezone`, or enabling NTP).

Executing `status` produces output showing local time, UTC (universal time), RTC time, timezone, NTP/RTC synchronization settings, and whether DST (daylight saving time) is active; giving a full snapshot of the system’s time configuration.

---

## Syntax

```
timedatectl status
```

Because `status` is the default command, invoking `timedatectl` without any command is equivalent to `timedatectl status`.

No arguments are needed. There are no optional flags specific to `status` in typical usage.

---

## Example

```bash
$ timedatectl status
```

This command will produce an output similar to:

```
               Local time: Thu 2025-11-27 14:45:23 EAT
           Universal time: Thu 2025-11-27 11:45:23 UTC
                 RTC time: Thu 2025-11-27 11:45:22
                Time zone: Africa/Nairobi (EAT, +0300)
    System clock synchronized: yes
              NTP service: active
          RTC in local TZ: no
```

---
