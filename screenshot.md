# Screenshot

Use `grim`. It is the recommended tool for taking screenshots in wayland compositors like Sway.

```sh
# Capture the entire screen.
grim screenshot.png
```

Use `slurp` to select a region.

```sh
# Capture a specific region.
grim -g "$(slurp)" screenshot.png
```
