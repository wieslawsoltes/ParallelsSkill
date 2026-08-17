# Host Networking and USB

`prlsrvctl` manages Parallels Desktop-wide resources. Changes may affect multiple VMs.

## Inspect Networks

```bash
prlsrvctl net list --json
prlsrvctl net info '<network-id>' --json
prlsrvctl net set --help
```

Network types commonly include shared/NAT, host-only, and bridged. Availability and option names vary by release and edition.

Before changing a network:

1. Resolve the exact network ID.
2. Record its full configuration.
3. Identify attached VM adapters with `prlctl list --info`.
4. Check for subnet overlap with host VPNs, LANs, and other virtual networks.
5. Inspect local help for DHCP, IPv4/IPv6, NAT, and port-forwarding syntax.
6. Apply one scoped change.
7. Verify host reachability, guest addressing, DNS, and required services.

Do not invent IP ranges or replace an active shared network without explicit requirements. Port forwarding can expose guest services; bind and firewall them deliberately.

## Configure a VM Adapter

VM adapters are managed with `prlctl set`, not host network commands:

```bash
prlctl set device_mgmt --help
prlctl set --device-add-net --help
prlctl set --device-set-net --help
```

Inspect the existing adapter name, MAC address, mode, and network binding before adding or changing it. Preserve MAC identity when a guest license, DHCP reservation, or service depends on it.

## Host USB Assignments

```bash
prlsrvctl usb list --help
prlsrvctl usb set --help
prlsrvctl usb del --help
```

Discover the exact host USB identity before assigning it. A host-wide assignment may redirect a device when attached and make it unavailable to macOS or another VM. Confirm the intended VM and recovery route before changing storage, security-token, phone, or input-device assignments.

For a currently running VM, device connect/disconnect may instead use `prlctl set "$VM" --device-connect ...` or `--device-disconnect ...`; inspect device-specific help first.
