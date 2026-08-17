# Snapshots, Clones, Archives, and Backups

## Snapshots

Create and immediately record the resulting snapshot inventory:

```bash
prlctl snapshot "$VM" --name '<purpose>' --description '<context>'
prlctl snapshot-list "$VM" --json
prlctl snapshot-list "$VM" --tree
```

Switch only by an exact discovered ID:

```bash
SNAPSHOT_ID='<snapshot-id>'
prlctl snapshot-switch "$VM" --id "$SNAPSHOT_ID"
```

Reverting discards state after that snapshot. Confirm the desired rollback point and whether the VM should resume. Snapshot deletion can affect descendants; inspect the tree and `snapshot-delete --help` before removal.

Snapshots share storage dependencies with the VM. They are useful rollback points, not independent backups.

## Full and Linked Clones

```bash
prlctl clone --help
prlctl clone "$VM" --name '<new-unique-name>' --dst '/absolute/destination'
```

A full clone is more independent but consumes more space. A linked clone is faster and smaller but depends on its parent/snapshot. Before cloning:

- confirm source state and consistency;
- choose a unique name and explicit destination;
- check available disk space;
- decide whether external disks should remain attached;
- understand UUID regeneration and identity implications.

After cloning, verify both source and clone registration, UUIDs, disk locations, networking identity, and boot behavior.

## Archives

```bash
prlctl archive --help
prlctl unarchive --help
```

Archiving changes VM storage/availability and may take significant time. Record the bundle location and state, ensure sufficient free space, and verify the archived or restored result. Do not treat an archive as a tested backup without a restore check.

## Backup Pattern

For a recoverable backup:

1. Prefer a clean guest shutdown.
2. Record VM configuration and bundle path.
3. Copy the complete `.pvm` bundle to separate storage using a method that preserves its contents.
4. Confirm copy completion and size.
5. When feasible, test registration or restoration under a distinct name without overwriting the original.

Never copy only selected files from inside a live bundle and call it a complete backup.
