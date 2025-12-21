```sh
# Install a display manager.
sudo pacman -S greetd

# Install a compositor/window manager.
sudo pacman -S sway swaybg waybar

# Install GPU drivers.
sudo pacman -S mesa intel-media-driver
```

Configure for wayland by creating a `.desktop` file in `/usr/share/wayland-sessions`:

```ini
# sway.desktop

[Desktop Entry]
Name=Sway
Comment=Wayland compositor
Exec=sway
Type=Application
```

---
