### Connect To A WiFi Network

Use `iwctl` to connect to the wifi network.

```bash
iwctl --passphrase PASSPHRASE station DEVICE connect SSID
# Example:
iwctl --passphrase "@wifi_passphrase" station wlan0 connect home_wifi
```

This will connect to the wifi network but won't get an IP address.

---

### Get an IP Address

Use `systemd-networkd` to get an IP address.
Create a config file in `/etc/systemd/network`. The file must end in `.network`. Have at least the following:

```
[Match]
name=wlan0

[Network]
DHCP=yes
```

Replace `wlan0` with your network interface.
Enable and start `systemd-networkd`.

```bash
systemctl enable --now systemd-networkd
```

---

### Network Name Resolution

You can now access the internet but without network name resolution.
`systemd-resolved` will handle that.

```bash
systemctl enable --now systemd-resolved
```

---
