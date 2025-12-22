## NTFS Read-Only Problem

### Driver

Linux has 2 drivers for mounting ntfs partitions:

1. Kernel NTFS driver (`ntfs`): Mostly read-only. Write support is experimental and dangerous.
2. `ntfs-3g` (FUSE-based): Has full read/write support.

Use the `ntfs-3g` driver for full read/write access.

```bash
sudo pacman -S install ntfs-3g

mount -t ntfs-3g /dev/sdXN /mnt
```

### Windows Hibernate

In dual-boot setups, ntfs partitions will be mounted as read-only if Windows didn't shutdown fully (hibernated).

---

## fstab

Use the UUID in `/etc/fstab` instead of device names because device names are a coincidence of hardware detection order and can change when other disks are added or removed.

```
# /dev/sda5
UUID=06DEEAF72A49A1E3	/mnt/data	ntfs	defaults	0	2
```

You can get the UUID using `lsblk`.

```sh
lsblk -o +UUID
```

---
