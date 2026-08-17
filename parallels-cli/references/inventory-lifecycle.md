# Inventory and Lifecycle

## Discover and Select

```bash
prlctl list --all --json
prlctl list --all --output uuid,name,status --no-header
prlctl list --info '<uuid-or-exact-name>'
prlctl status '<uuid-or-exact-name>'
```

Use the UUID for automation. Names can contain spaces and may be renamed. Use `prlctl list -L` to discover output-field names supported by the installed release.

## Create or Register

Inspect operating-system values before creation:

```bash
prlctl create --help
prlctl create '<new-name>' --ostype <type> --distribution <distribution>
```

Register an existing VM bundle without copying it:

```bash
prlctl register '/absolute/path/to/example.pvm'
```

The register command may support UUID regeneration, preservation, forced registration, and a new name. Check local help before using those options.

## Power Operations

```bash
prlctl start "$VM"
prlctl pause "$VM"
prlctl resume "$VM"
prlctl suspend "$VM"
prlctl restart "$VM"
prlctl stop "$VM"
```

After each operation, poll `prlctl status "$VM"` until it reaches the intended state or a reasonable timeout. Do not assume the command's return means the guest has fully booted or shut down.

Forced operations are last resorts:

```bash
prlctl stop --help
prlctl reset --help
```

Before force-stopping or resetting, capture a screenshot and record current status. Warn that unsaved guest data may be lost.

## Move, Convert, Archive, and Unarchive

These commands affect storage layout or VM availability:

```bash
prlctl move --help
prlctl convert --help
prlctl archive --help
prlctl unarchive --help
```

Resolve absolute source/destination paths, check free space, shut down when required, preserve a recoverable copy, and verify registration plus guest boot afterward.

## Unregister Versus Delete

```bash
prlctl unregister "$VM"
prlctl delete "$VM"
```

- `unregister` removes the VM from Parallels Desktop while leaving the bundle on the host filesystem.
- `delete` removes the VM and is destructive.

Do not use either command merely to hide or stop a VM. Before unregistering, record the bundle path from `prlctl list --info`. Before deletion, require clear authorization and verify any required backup.
