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

### rfkill

Sometimes, the network interface like `wlan0` can suddenly go DOWN. This can be caused when rfkill is blocking the device. To test if rfkill is blocking the device, run these commands:

```sh
ip link set wlan0 up
# Expected output: RTNETLINK answers: Operation not possible due to RF-kill

rfkill list
# Expected output:
#0: hci0: Bluetooth
#        Soft blocked: yes
#        Hard blocked: no
#1: phy0: Wireless LAN
#        Soft blocked: yes
#        Hard blocked: no
```

To fix this, run:

```sh
rfkill unblock wifi

ip link wlan0 up
# If it doesn't go up automatically.
```

Confirm:

```sh
ip link show wlan0
```

---
