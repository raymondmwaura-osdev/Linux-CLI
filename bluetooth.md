# `bluetooth`

Install required packages:

```sh
sudo pacman -S bluez bluez-utils
```

Enable and start the bluetooth service:

```sh
systemctl enable --now bluetooth.service
```

Use `bluetoothctl` to connect to devices:

```
power on
scan on
pair [MAC_ADDRESS]
connect [MAC_ADDRESS]
```

---
