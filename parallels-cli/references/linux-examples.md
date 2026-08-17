# Linux VM Examples

Set a generic target discovered from `prlctl list --all --json`:

```bash
VM='<linux-vm-uuid-or-name>'
prlctl status "$VM"
prlctl list --info "$VM"
```

## Read-Only Guest Checks

```bash
prlctl exec "$VM" --current-user uname -a
prlctl exec "$VM" --current-user /usr/bin/id
prlctl exec "$VM" --current-user /bin/sh -lc 'cat /etc/os-release'
prlctl exec "$VM" --current-user /bin/sh -lc 'df -h /'
```

Use `/bin/sh -lc` only when shell syntax is needed. Confirm paths with `command -v` inside the guest because distributions differ.

## Service Inspection

For a systemd guest:

```bash
prlctl exec "$VM" --current-user systemctl --no-pager --failed
prlctl exec "$VM" --current-user systemctl --no-pager status '<unit>'
```

Do not assume systemd on every Linux distribution. Detect the init system first.

## Package Work

Detect the distribution and package manager before installing anything. Do not embed sudo passwords. For an Ubuntu/Debian guest, an authorized example is:

```bash
prlctl exec "$VM" --current-user sudo apt-get update
prlctl exec "$VM" --current-user sudo apt-get install -y '<package>'
```

For Fedora/RHEL-derived guests use the discovered `dnf` or compatible manager. Package installation is a guest mutation: identify packages, repositories, architecture, and expected disk/network impact first.

## Shared Script Pattern

Share a narrow host directory read-only:

```bash
prlctl set "$VM" --shf-host-add automation \
  --path '/absolute/host/automation' --mode ro
```

Find the Parallels shared-folder mount inside the guest rather than assuming its path, then invoke the script explicitly. Use Unix line endings and an appropriate shebang.

## Architecture Checks

```bash
prlctl exec "$VM" --current-user uname -m
```

On supported Apple-silicon hosts, inspect `prlctl set advanced --help` before enabling Rosetta for Linux. Do not assume x86_64 binaries can run in every ARM Linux VM.

## Verify a GUI Application

1. Confirm the guest graphical session is active.
2. Start the app through a guest command appropriate to that desktop session.
3. Capture the VM display to a temporary PNG.
4. Inspect the screenshot.
5. Use key events only when direct guest commands cannot reach the required UI.
6. Capture again and report visible evidence plus limitations.
