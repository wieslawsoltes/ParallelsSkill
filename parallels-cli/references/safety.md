# Safety and Recovery

## Classify the Operation

Read-only:

- versions, help, VM/network lists, status, configuration inspection, snapshot lists, screenshots;
- harmless guest inspection commands.

Reversible but consequential:

- start, graceful stop, pause/resume, suspend, attach/detach devices, shared-folder settings;
- snapshots and most configuration changes when a known rollback exists.

Potentially destructive or hard to reverse:

- delete, snapshot deletion, reset, forced stop, drop-state operations;
- virtual-disk removal/resizing/format conversion;
- encryption, TPM, Secure Boot, protection, and password changes;
- move/convert/archive, unregister without recording the bundle path;
- host virtual-network changes and USB reassignment.

## Preflight for Consequential Work

1. Discover the exact UUID and current state.
2. Record `prlctl list --info` and relevant snapshot/network/device inventories.
3. Clarify the intended end state and acceptable downtime.
4. Identify guest applications or services that need a clean shutdown.
5. Confirm recovery material and an independent backup when data is at risk.
6. Inspect local help for the installed Parallels version.
7. Use explicit quoted targets and absolute paths.

## Verification

After a mutation:

- re-read VM or host configuration;
- wait for the intended lifecycle state;
- verify guest boot and Tools status when applicable;
- verify networking from both sides;
- inspect disks and shared folders in the guest;
- preserve command output needed for an audit, excluding secrets.

## Recovery Order

1. Capture current visible and CLI state.
2. Try a graceful guest or lifecycle action.
3. Use an existing verified snapshot only with approval to discard later state.
4. Restore from an independent backup when snapshot integrity or storage is uncertain.
5. Use forced reset/stop only when graceful recovery is unavailable and data-loss risk is accepted.

Do not improvise filesystem edits inside a `.pvm` bundle. Use supported Parallels operations or restore a known-good copy.
