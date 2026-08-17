# Parallels Desktop Administration

`prlsrvctl` operates at the host application level. Inspect `prlsrvctl --help` before using it because many actions affect every VM or the Parallels installation.

## Information and Preferences

```bash
prlsrvctl info --json
prlsrvctl set --help
```

Desktop information may include licensing, host identifiers, filesystem locations, devices, and network interfaces. Review and redact it before sharing.

Preference options vary by release and can include memory limits, security level, telemetry, logging, screenshot attachment, password requirements, settings locks, and external-device behavior. Record the current value, change only the requested option, and verify with a fresh information read.

## Parallels Users and Default VM Home

```bash
prlsrvctl user list --json
prlsrvctl user set --help
```

Changing the default VM home controls where future VMs are created; it does not necessarily move existing bundles. Use an absolute destination with adequate free space and permissions. Inspect each existing VM's home path separately before planning migration.

## Licensing

The CLI can install, update, defer, or deactivate a license. These actions affect product access and may affect an account or managed deployment.

- Perform licensing actions only on explicit request.
- Never paste or persist a license key in scripts, repositories, logs, or chat.
- Be aware that an argument such as `--key` can be visible to process inspection and shell history.
- Prefer a user-controlled secure terminal or an organization's approved license deployment mechanism.
- Do not print full license information in routine diagnostics.

## Problem Reports

```bash
prlsrvctl problem-report --help
prlctl problem-report --help
```

Reports can collect host/guest configuration, logs, contact information, and screenshots. Create or send one only when requested, minimize optional attachments, and inspect the archive for sensitive data before sharing. Sending a report is an external side effect and needs explicit authorization.

## Desktop Service Shutdown

```bash
prlsrvctl shutdown --help
```

This is host-wide and can disrupt all registered VMs. Inventory their states, gracefully shut down or suspend the intended guests, confirm downtime, and use forced shutdown only when the risk is accepted.
