# Guest Operations and Console Automation

## Preconditions

Check that the VM is running and Parallels Tools is installed:

```bash
prlctl status "$VM"
prlctl list --info "$VM"
prlctl exec --help
```

If Tools is missing or stale, inspect `prlctl installtools --help`. Installation can mount media or prompt inside the guest, so do not assume it is unattended.

## Execute Commands

Preferred form:

```bash
prlctl exec "$VM" --current-user <executable> <arg>...
```

Use direct executable arguments for deterministic behavior. For shell features:

```bash
prlctl exec "$VM" --current-user /bin/sh -lc '<guest-shell-command>'
prlctl exec "$VM" --current-user powershell.exe -NoProfile -Command '<powershell-command>'
```

Keep the shell payload a single guest argument. Quoting passes through the host shell and the Parallels transport, so test complex quoting with a harmless command first. For long scripts, put a script in a narrowly scoped shared folder and execute the guest path.

Avoid `--password`: process listings and logs can expose it. Prefer `--current-user` when a suitable guest session exists, interactive authentication, or an approved external secret mechanism.

## Interactive Entry

`prlctl enter "$VM"` opens an interactive guest session when supported. Use it for diagnosis, not scripts. Exit cleanly and avoid changing terminal state unexpectedly.

## Screenshots

```bash
SHOT="$(mktemp -d -t parallels-screen)/screen.png"
prlctl capture "$VM" --file "$SHOT"
```

Capture requires a running/displayable VM. Use an absolute output path, inspect the PNG with the available image viewer, and avoid screenshots when secrets or personal data are visible.

## Keyboard Input

```bash
prlctl send-key-event --help
prlctl send-key-event "$VM" --key <virtual-keycode>
```

The command accepts Parallels virtual key codes or scan codes and can send press/release events with a delay. Confirm current syntax and codes before sending input. Take a screenshot before and after every meaningful input sequence.

Prefer `prlctl exec` over simulated typing. Use keyboard automation for firmware, boot menus, recovery screens, installers, login screens, or GUI state that guest execution cannot reach.

## Troubleshooting Order

1. Confirm target UUID and VM state.
2. Inspect `GuestTools` in `prlctl list --info`.
3. Test a harmless direct executable such as `uname` or `cmd.exe /c ver`.
4. Check whether the requested user has an active session when using `--current-user`.
5. Simplify quoting and remove shell layers.
6. Capture the console for visible prompts or errors.
7. Use `prlctl problem-report --help` or `prlsrvctl problem-report --help` only when a diagnostic report is needed; review it for sensitive data before sharing.
