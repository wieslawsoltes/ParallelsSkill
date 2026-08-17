# Parallels CLI Skill for Codex

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

A portable Codex skill for automating Parallels Desktop for Mac through its official command-line tools. It helps Codex inspect and operate Linux and Windows virtual machines with safe, deterministic `prlctl` and `prlsrvctl` workflows.

## Capabilities

- Discover VMs, UUIDs, state, configuration, networks, and snapshots.
- Create, register, start, stop, suspend, clone, move, archive, and unregister VMs.
- Configure CPU, memory, startup behavior, disks, network adapters, USB devices, and shared folders.
- Execute commands inside Linux and Windows guests through Parallels Tools.
- Capture screenshots and send keyboard events for installer, recovery, and GUI automation.
- Configure sharp GNOME Retina/HiDPI output with Parallels high-resolution video and integer scaling.
- Manage snapshots, full or linked clones, archives, backups, virtual networks, and host-wide preferences.
- Diagnose Parallels installations with a reusable read-only helper.
- Apply explicit safety checks around deletion, forced shutdown, storage, encryption, licensing, and network changes.

The skill uses installed CLI help as the exact syntax authority because available flags can vary between Parallels Desktop releases. Its reference guides are grounded in the official [Parallels Desktop Developer's Guide](https://docs.parallels.com/parallels-desktop-developers-guide/command-line-interface-utility/manage-virtual-machines-from-cli) and [Command-Line Reference](https://download.parallels.com/desktop/v20/docs/en_US/Parallels%20Desktop%20Command-Line%20Reference.pdf).

## Requirements

- A macOS host with Parallels Desktop installed.
- `prlctl` and `prlsrvctl` available in `PATH`.
- Parallels Tools installed in guests for guest command execution and most integration features.
- Codex with skill support.

Linux and Windows are supported as guest operating systems. Parallels Desktop and its CLI execute on the macOS host, not inside the VM.

## Installation

Clone this repository and copy the `parallels-cli` directory into the skills directory of the Codex installation that should use it:

```bash
git clone https://github.com/wieslawsoltes/ParallelsSkill.git
mkdir -p "${CODEX_HOME:-$HOME/.codex}/skills"
cp -R ParallelsSkill/parallels-cli "${CODEX_HOME:-$HOME/.codex}/skills/"
```

Restart Codex or start a new session after installation. For custom Codex profiles, set `CODEX_HOME` to that profile's root before copying.

## Usage

Invoke the skill explicitly:

```text
$parallels-cli List every VM and summarize its state and Parallels Tools status.
```

Example requests:

```text
$parallels-cli Start my Linux development VM, verify it booted, and show its IP addresses.

$parallels-cli Run a read-only PowerShell inventory in my Windows VM.

$parallels-cli Create a named snapshot before changing the VM's CPU and memory allocation.

$parallels-cli Capture the current VM display and diagnose why the installer is blocked.

$parallels-cli Make GNOME text sharp at macOS-like Retina density without fractional scaling.

$parallels-cli Inspect shared and host-only networks without changing them.
```

Codex should discover the exact VM name or UUID rather than relying on the placeholder names used in documentation.

## Read-Only Diagnostics

The included doctor script checks the host, installed CLI versions, registered VMs, and virtual networks without changing them:

```bash
./parallels-cli/scripts/parallels-doctor
./parallels-cli/scripts/parallels-doctor --vm '<name-or-uuid>'
```

It requires macOS and exits if the Parallels CLI is not available.

## Repository Structure

```text
parallels-cli/
├── SKILL.md
├── agents/
│   └── openai.yaml
├── scripts/
│   └── parallels-doctor
└── references/
    ├── configuration-devices.md
    ├── desktop-administration.md
    ├── guest-operations.md
    ├── host-networking.md
    ├── inventory-lifecycle.md
    ├── linux-examples.md
    ├── linux-retina-scaling.md
    ├── official-docs.md
    ├── safety.md
    ├── snapshots-clones-backups.md
    └── windows-examples.md
```

The main [SKILL.md](parallels-cli/SKILL.md) contains the operating workflow and routes Codex to focused references only when needed.

## Safety Model

The skill distinguishes read-only inspection, reversible VM operations, and potentially destructive work. It requires exact target discovery and verification around actions such as:

- deleting or unregistering VMs;
- removing snapshots or virtual disks;
- resetting or force-stopping a guest;
- moving, converting, encrypting, or archiving VM storage;
- changing TPM, Secure Boot, licensing, virtual networks, or USB assignments.

Passwords, tokens, license keys, and recovery keys must not be embedded in commands, logs, screenshots, examples, or repository files.

## Validation

Validate the skill with the validator bundled with Codex's `skill-creator` skill:

```bash
python3 /path/to/skill-creator/scripts/quick_validate.py parallels-cli
sh -n parallels-cli/scripts/parallels-doctor
```

The diagnostics helper can then be smoke-tested on the target macOS host. Its default workflow is read-only.

## Contributing

Contributions are welcome. Keep examples portable:

- use placeholders for VM names, UUIDs, users, paths, network IDs, and snapshot IDs;
- support both Linux and Windows guests where behavior differs;
- verify command syntax against current local `--help` output;
- cite first-party Parallels documentation for new workflows;
- avoid machine-specific paths, credentials, and environment assumptions.

## License

Licensed under the [MIT License](LICENSE).

Parallels and Parallels Desktop are trademarks of Parallels International GmbH. This project is an independent community project and is not affiliated with or endorsed by Parallels.
