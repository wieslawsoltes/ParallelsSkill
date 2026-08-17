# Official Parallels Documentation

Use the installed command help as the exact syntax reference for that host. Use these first-party Parallels sources for concepts, workflows, and release-specific behavior.

## Primary CLI Sources

- [Manage Virtual Machines from CLI](https://docs.parallels.com/parallels-desktop-developers-guide/command-line-interface-utility/manage-virtual-machines-from-cli) — current Developer's Guide entry point for `prlctl`.
- [Parallels Desktop Command-Line Reference, version 20 PDF](https://download.parallels.com/desktop/v20/docs/en_US/Parallels%20Desktop%20Command-Line%20Reference.pdf) — broad command catalog for `prlctl` and `prlsrvctl`; validate every option against the installed release.
- [Parallels Desktop resources](https://www.parallels.com/products/desktop/resources/) — product documentation landing page referenced by current CLI help.
- [Parallels Desktop preferences](https://docs.parallels.com/landing/parallels-desktop-developers-guide/command-line-interface-utility/manage-parallels-desktop-from-cli/parallels-desktop-preferences) — host-wide preference scope for `prlsrvctl`.

## Focused Procedures

- [Register or unregister a virtual machine](https://docs.parallels.com/landing/parallels-desktop-developers-guide/command-line-interface-utility/manage-virtual-machines-from-cli/general-virtual-machine-management/register-unregister-a-virtual-machine)
- [Revert to a snapshot](https://docs.parallels.com/landing/parallels-desktop-developers-guide/command-line-interface-utility/manage-virtual-machines-from-cli/snapshot-management/reverting-to-a-snapshot)
- [Capture a screen area](https://docs.parallels.com/landing/parallels-desktop-developers-guide/command-line-interface-utility/manage-virtual-machines-from-cli/general-virtual-machine-management/capture-a-screen-area)
- [Common virtual-device options](https://docs.parallels.com/landing/parallels-desktop-developers-guide/command-line-interface-utility/manage-virtual-machines-from-cli/virtual-machine-configuration-tasks/device-management/common-options)
- [Persist 200% GNOME scaling in a Linux Arm VM](https://kb.parallels.com/129534)

## Current User-Guide Context

- [Clone a virtual machine](https://docs.parallels.com/landing/pdfm-ug/parallels-desktop-for-mac-26-users-guide/advanced-topics/working-with-virtual-machines/cloning-a-virtual-machine)
- [Back up a virtual machine](https://docs.parallels.com/landing/pdfm-ug/parallels-desktop-for-mac-26-users-guide/advanced-topics/working-with-virtual-machines/backing-up-a-virtual-machine)
- [Launch Windows](https://docs.parallels.com/landing/pdfm-ug/parallels-desktop-for-mac-26-users-guide/use-windows-on-your-mac/getting-started/launching-windows)
- [Windows startup and shutdown settings](https://docs.parallels.com/landing/pdfm-ug/parallels-desktop-for-mac-26-users-guide/use-windows-on-your-mac/getting-started/shutdown-and-suspend-windows/startup-and-shutdown-settings)
- [Connect external devices](https://docs.parallels.com/landing/pdfm-ug/parallels-desktop-for-mac-26-users-guide/use-windows-on-your-mac/connecting-external-devices)
- [Graphics settings and Retina modes](https://docs.parallels.com/landing/pdfm-ug/parallels-desktop-for-mac-26-users-guide/parallels-desktop-preferences-and-virtual-machine-settings/virtual-machine-settings/hardware-settings/graphics-settings)

## Version Discipline

1. Record `prlctl --version`. Obtain the `prlsrvctl` version from the first line of `prlsrvctl --help` when that release has no version action.
2. Read `command --help` and, for configuration, the relevant category help such as `prlctl set device_mgmt --help`.
3. Treat the current local help as authoritative for flags and accepted values.
4. Use the official guides for semantics, then note any observed release difference.
5. Do not copy examples from third-party blogs when the installed help or first-party documentation covers the operation.
