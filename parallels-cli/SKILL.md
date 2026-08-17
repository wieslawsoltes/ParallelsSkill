---
name: parallels-cli
description: Automate Parallels Desktop for Mac and its Linux or Windows virtual machines with prlctl and prlsrvctl. Use for VM discovery, lifecycle and configuration, guest command execution, screenshots and keyboard input, snapshots, clones, archives, host networking, shared folders, devices, Retina and HiDPI display scaling, troubleshooting, and safe scriptable workflows.
---

# Parallels CLI

Operate Parallels Desktop from the macOS host with deterministic, inspectable commands. Treat installed CLI help as the executable source of truth because options vary by Parallels Desktop release.

## Start Safely

1. Confirm the host and CLI versions:

   ```bash
   uname -s
   prlctl --version
   prlsrvctl --help 2>&1 | sed -n '1p'
   ```

2. Discover targets instead of guessing names or paths:

   ```bash
   prlctl list --all --json
   prlsrvctl net list --json
   ```

3. Select a VM by UUID when names are ambiguous, then inspect it:

   ```bash
   VM='<uuid-or-exact-name>'
   prlctl status "$VM"
   prlctl list --info "$VM"
   ```

4. Before a mutating command, open its local help and record relevant state:

   ```bash
   prlctl snapshot --help
   prlctl snapshot-list "$VM" --json
   ```

5. Perform the smallest scoped action and verify the resulting state.

Run `scripts/parallels-doctor` for a read-only host, network, and VM inventory. Pass `--vm '<name-or-uuid>'` to inspect one VM.

## Choose the Right Utility

- Use `prlctl` for individual VMs: create, register, inspect, power, configure, execute, capture, snapshot, clone, move, archive, and delete.
- Use `prlsrvctl` for Parallels Desktop and host-wide resources: product information, preferences, users, virtual networks, USB assignments, problem reports, and service shutdown.
- Use commands inside the guest only after confirming its state and Parallels Tools availability.

## Core Workflow

### Inventory and inspect

Prefer JSON for automation and human-readable output for diagnosis:

```bash
prlctl list --all --json
prlctl list --info "$VM"
prlctl snapshot-list "$VM" --tree
prlsrvctl info --json
```

Never parse the column spacing of default tables when JSON or explicit output fields are available. Treat `prlsrvctl info` as potentially sensitive host output and do not publish it unreviewed.

### Manage lifecycle

Use graceful operations first:

```bash
prlctl start "$VM"
prlctl pause "$VM"
prlctl resume "$VM"
prlctl suspend "$VM"
prlctl restart "$VM"
prlctl stop "$VM"
```

`reset`, `stop --kill`, and state-dropping options can lose guest work. Use them only after authorization or when the request explicitly calls for forced recovery. See [references/inventory-lifecycle.md](references/inventory-lifecycle.md).

### Configure a VM

Inspect the relevant option group before changing it:

```bash
prlctl set cpus --help
prlctl set memory --help
prlctl set device_mgmt --help
prlctl set shared_folders --help
```

Most settings use `prlctl set "$VM" <options>`. Some hardware changes require a stopped VM. Capture before/after `prlctl list --info "$VM"` output and do not assume settings are hot-pluggable. See [references/configuration-devices.md](references/configuration-devices.md).

For sharp GNOME text on a Retina Mac, combine the Parallels high-resolution framebuffer with integer 200% guest scaling. Read [references/linux-retina-scaling.md](references/linux-retina-scaling.md) before changing video or GNOME display settings.

### Operate inside a guest

Use Parallels Tools-backed execution for shell or PowerShell work:

```bash
prlctl exec "$VM" --current-user <program> <arg>...
```

Pass an executable and arguments directly. Use a guest shell only for pipes, redirection, expansion, or compound statements. Never expose passwords or tokens in command arguments, logs, screenshots, or repository files. See [references/guest-operations.md](references/guest-operations.md), [references/linux-examples.md](references/linux-examples.md), and [references/windows-examples.md](references/windows-examples.md).

### Capture and drive the console

```bash
prlctl capture "$VM" --file /absolute/path/to/screen.png
prlctl send-key-event "$VM" --key <virtual-keycode>
```

Capture before and after input. Prefer guest execution for deterministic automation; use key events for boot screens, installers, login recovery, or GUI-only flows. Confirm accepted key codes with local help and current documentation.

### Protect and copy state

Snapshots are convenient rollback points, not independent backups. Full clones or copied stopped VM bundles provide stronger separation. Archives change how a VM bundle is stored and used. See [references/snapshots-clones-backups.md](references/snapshots-clones-backups.md).

### Manage host networking

Discover network IDs before using them:

```bash
prlsrvctl net list --json
prlsrvctl net info '<network-id>' --json
```

Network mutations can disconnect multiple VMs. Inspect the selected network and its local `set` help, identify affected guests, then verify connectivity from both host and guest. See [references/host-networking.md](references/host-networking.md).

## Destructive-Action Rules

- Resolve the exact VM UUID, path, snapshot ID, device, or network before changing it.
- Treat `delete`, `snapshot-delete`, forced stop/reset, disk removal, encryption changes, archive/unarchive, move/convert, and network reconfiguration as consequential.
- Distinguish `unregister` from `delete`: unregister removes a VM from Parallels Desktop registration but leaves its bundle on disk; delete removes the VM.
- Do not remove snapshots until dependencies and desired rollback points are understood.
- Prefer a graceful shutdown and a recoverable copy before disk, encryption, or migration work.
- Never store guest or Parallels credentials in shell history. Avoid `--password`; use `--current-user`, an interactive flow, or a secure external secret mechanism.
- Stop and ask when the target, data-loss boundary, or desired end state is ambiguous.

Read [references/safety.md](references/safety.md) before recovery, deletion, migration, or host-wide changes.

## Reference Routing

- Official Parallels sources and version strategy: [references/official-docs.md](references/official-docs.md)
- Desktop information, preferences, users, licensing, reports, and service control: [references/desktop-administration.md](references/desktop-administration.md)
- Inventory, creation, registration, power, movement, and deletion: [references/inventory-lifecycle.md](references/inventory-lifecycle.md)
- CPU, memory, boot, disks, optical media, adapters, USB, and sharing: [references/configuration-devices.md](references/configuration-devices.md)
- Guest commands, Tools, screenshots, input, and troubleshooting: [references/guest-operations.md](references/guest-operations.md)
- Snapshots, clones, archives, and backups: [references/snapshots-clones-backups.md](references/snapshots-clones-backups.md)
- Desktop-wide networks and USB assignments: [references/host-networking.md](references/host-networking.md)
- Linux recipes: [references/linux-examples.md](references/linux-examples.md)
- Linux GNOME Retina and HiDPI scaling: [references/linux-retina-scaling.md](references/linux-retina-scaling.md)
- Windows recipes: [references/windows-examples.md](references/windows-examples.md)
- Risk classification and recovery practices: [references/safety.md](references/safety.md)

## Portability Requirements

- Use placeholders or variables for VM names, UUIDs, users, network IDs, paths, and snapshot IDs.
- Do not assume Intel or Apple silicon, a particular guest distribution, a default VM directory, or a specific Parallels edition.
- Discover capabilities from the installed version. If published documentation and `--help` differ, follow `--help` and report the version-dependent difference.
- Quote all user-controlled names and paths.
- Verify every mutation with a fresh read rather than inferring success from exit status alone.
