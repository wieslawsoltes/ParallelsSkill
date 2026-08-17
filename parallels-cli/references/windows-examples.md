# Windows VM Examples

Set a generic target discovered from `prlctl list --all --json`:

```bash
VM='<windows-vm-uuid-or-name>'
prlctl status "$VM"
prlctl list --info "$VM"
```

## Read-Only Guest Checks

```bash
prlctl exec "$VM" --current-user cmd.exe /d /s /c ver
prlctl exec "$VM" --current-user whoami.exe
prlctl exec "$VM" --current-user powershell.exe -NoProfile -NonInteractive \
  -Command '$PSVersionTable.PSVersion.ToString()'
```

PowerShell exists as `powershell.exe` on Windows PowerShell installations. Use `pwsh.exe` only after detecting PowerShell 7.

## Operating-System and Service Inspection

```bash
prlctl exec "$VM" --current-user powershell.exe -NoProfile -NonInteractive \
  -Command 'Get-CimInstance Win32_OperatingSystem | Select-Object Caption,Version,OSArchitecture | Format-List'
prlctl exec "$VM" --current-user powershell.exe -NoProfile -NonInteractive \
  -Command 'Get-Service | Where-Object Status -eq Running | Select-Object -First 10 Name,Status'
```

Complex PowerShell quoting crosses the macOS shell and Parallels transport. Test with a harmless expression first. For long scripts, use a narrowly scoped shared folder and invoke a signed or otherwise trusted `.ps1` file under the guest's execution policy.

## Package Work

Detect whether `winget.exe` is available:

```bash
prlctl exec "$VM" --current-user where.exe winget.exe
```

Before an authorized install, identify the exact package ID and source. A typical pattern is:

```bash
prlctl exec "$VM" --current-user winget.exe install --exact \
  --id '<package-id>' --accept-source-agreements --accept-package-agreements
```

Package installation can prompt, reboot, or require elevation. Do not assume `--current-user` is elevated, bypass UAC, or place credentials on the command line.

## Windows Paths

When the host shell would interpret `$`, backslashes, spaces, or wildcard characters, quote the entire PowerShell command with host-side single quotes. Prefer PowerShell literal paths in the guest:

```bash
prlctl exec "$VM" --current-user powershell.exe -NoProfile -NonInteractive \
  -Command 'Get-Item -LiteralPath "C:\Program Files"'
```

## GUI and Recovery Flows

1. Capture the current display.
2. Inspect whether Windows is at boot, lock, UAC, desktop, or recovery UI.
3. Prefer guest execution when Tools and a session are available.
4. Use key events only for UI that cannot be controlled by guest commands.
5. Never type a password or recovery key into a logged command; use a user-controlled secure entry path.
6. Capture the result and report exactly what is visible.

For Windows security changes, preserve BitLocker recovery material and understand TPM/Secure Boot effects before changing VM configuration.
