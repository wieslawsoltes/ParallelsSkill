# Linux GNOME Retina Scaling

Use a high-resolution Parallels framebuffer with GNOME's integer 200% scale to obtain sharp text at macOS-like Retina density. Avoid fractional scaling because compositor resampling can make text appear blurry.

## Inspect First

Run on the macOS host:

```bash
VM='<linux-vm-name-or-uuid>'

prlctl status "$VM"
prlctl list --info "$VM"
prlctl set video --help
```

Confirm that the VM is running, Parallels Tools is installed, and the installed Parallels release supports the video flags below.

## Configure the Parallels Framebuffer

Run on the macOS host:

```bash
prlctl set "$VM" --high-resolution on
prlctl set "$VM" --high-resolution-in-guest on
prlctl set "$VM" --native-scaling-in-guest off
```

Verify instead of relying only on exit status:

```bash
prlctl list --info "$VM" | grep -i video
```

Use `high-resolution=on`, `high-resolution-in-guest=on`, and `native-scaling-in-guest=off` as the known-good baseline for GNOME-managed integer scaling.

## Configure GNOME

Change settings as the logged-in desktop user, not as root. In **Settings → Displays**, select **200%**. Do not enable fractional scaling.

Run in that user's graphical session to make the integer scale persistent across Parallels dynamic-resolution changes:

```bash
gsettings set org.gnome.desktop.interface scaling-factor 2
gsettings set org.gnome.desktop.interface text-scaling-factor 1.0
```

Inspect Mutter's experimental features:

```bash
gsettings get org.gnome.mutter experimental-features
```

If `scale-monitor-framebuffer` is enabled only for fractional scaling, disable fractional scaling in GNOME Displays. Do not overwrite the entire experimental-feature list when unrelated features are intentionally enabled. A system with no enabled experimental features reports `@as []`.

For host-driven automation, use `prlctl exec "$VM" --current-user` only when it resolves to the active GNOME desktop user and inherits a usable session bus. Otherwise execute the commands in a guest terminal or explicitly target that user's graphical session; changing root's dconf database does not configure the desktop user.

## Verify

```bash
gsettings get org.gnome.desktop.interface scaling-factor
gsettings get org.gnome.desktop.interface text-scaling-factor
gsettings get org.gnome.mutter experimental-features
```

Expect `uint32 2`, `1.0`, and no fractional-scaling feature. Capture and inspect the VM after the change:

```bash
CAPTURE_DIR="$(mktemp -d)"
CAPTURE="$CAPTURE_DIR/parallels-retina.png"
prlctl capture "$VM" --file "$CAPTURE"
```

The physical framebuffer should be approximately twice the logical desktop dimensions. For example, `3600×2016` physical corresponds to a `1800×1008` logical workspace. Window resizing can change the exact physical dimensions.

If an application retains its old scale, close and reopen it or sign out and back in. Do not switch to a lower-resolution scaled framebuffer merely to enlarge text; that sacrifices sharpness.

## Roll Back

Select **100%** in GNOME Displays, then reset the persistent integer scale as the desktop user:

```bash
gsettings reset org.gnome.desktop.interface scaling-factor
```

To disable the Retina framebuffer too, run on the macOS host:

```bash
prlctl set "$VM" --high-resolution off
```

Re-read `prlctl list --info "$VM"` and the GNOME settings after rollback.

## Official Sources

- [Parallels: Change scaling in a GNOME Linux VM on Apple silicon](https://kb.parallels.com/129534)
- [Parallels Desktop graphics settings](https://docs.parallels.com/landing/pdfm-ug/parallels-desktop-for-mac-26-users-guide/parallels-desktop-preferences-and-virtual-machine-settings/virtual-machine-settings/hardware-settings/graphics-settings)
