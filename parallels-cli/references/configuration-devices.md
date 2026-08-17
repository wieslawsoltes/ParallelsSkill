# VM Configuration and Devices

## Inspect Before Changing

```bash
prlctl list --info "$VM"
prlctl set --help
prlctl set cpus --help
prlctl set memory --help
prlctl set boot --help
prlctl set startup --help
```

Settings generally use this shape:

```bash
prlctl set "$VM" <option> <value>
```

Examples whose accepted values must be confirmed locally:

```bash
prlctl set "$VM" --cpus auto
prlctl set "$VM" --memsize auto
prlctl set "$VM" --startup-view headless
```

Shut down the VM first when local help or an error says a setting is not hot-changeable. Re-read VM information after each change.

## Device Workflow

1. Inspect `prlctl set device_mgmt --help`.
2. Inspect device-specific help, for example `prlctl set --device-add-hdd --help`.
3. Identify the exact device from `prlctl list --info "$VM"`.
4. Add, set, connect, disconnect, or remove only that device.
5. Verify configuration and guest visibility.

The main device verbs are `--device-add`, `--device-set`, `--device-connect`, `--device-disconnect`, and `--device-del`. Supported device types include disks, optical drives, floppy drives, network adapters, serial/parallel ports, sound, and USB support.

Deleting a virtual disk can destroy data. Determine whether the command only detaches the disk or also removes its backing file before proceeding.

## Shared Folders

Inspect sharing options:

```bash
prlctl set shared_folders --help
```

Add a named read-only host share:

```bash
prlctl set "$VM" --shf-host-add '<share-name>' \
  --path '/absolute/host/path' --mode ro
```

Prefer read-only sharing unless the guest must write. Quote names and paths, avoid broad host directories, and verify the mount inside the guest. Parallels Tools is normally required.

## Guest-Specific Settings

Some options are operating-system or architecture dependent. For example, hostname synchronization is Linux-only, and Rosetta for Linux applies only to supported Apple-silicon configurations. Inspect `prlctl set advanced --help` and report unsupported combinations rather than forcing them.

## Security and Encryption

Before changing TPM, secure boot, encryption, password protection, or isolation:

- confirm guest requirements such as Windows BitLocker recovery material;
- ensure a separate backup exists;
- determine whether the VM must be stopped;
- never place secrets in command-line arguments or logs;
- verify the VM can still boot and unlock after the change.
