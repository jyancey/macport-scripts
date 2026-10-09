# MacPorts Scripts

Two defensive Zsh utilities for maintaining or removing a [MacPorts](https://www.macports.org/) installation on macOS:

- `macportsupdate` updates MacPorts, upgrades outdated ports, and performs optional maintenance.
- `macportsremove` removes installed ports and known MacPorts system integration files.

> [!WARNING]
> `macportsremove` is destructive. Run it with `--dry-run` first and review the paths and commands before confirming removal.

## Requirements

- macOS with [MacPorts](https://www.macports.org/install.php) installed
- Zsh (the scripts use `/usr/bin/env zsh`)
- `sudo` for mutating operations when running as a regular user
- `dscl` for checking and removing the MacPorts account and group

Both scripts can be run as a regular user. Read-only checks and dry-runs do not require elevation. When a real mutating operation is requested, the scripts validate `sudo` access and elevate only the individual command that needs it.

## Installation

Clone or copy the scripts to a directory on your `PATH`, then make sure they are executable:

```console
chmod +x macportsupdate macportsremove
```

Alternatively, run them from this directory:

```console
./macportsupdate --check
./macportsremove --dry-run
```

## Updating MacPorts

Run the default workflow:

```console
./macportsupdate
```

The default workflow:

1. Validates the MacPorts installation.
2. Runs `port selfupdate`.
3. Lists outdated ports.
4. Runs `port upgrade outdated`.
5. Reports the results.

Preview mutating commands without changing the installation:

```console
./macportsupdate --dry-run
```

Run diagnostics and inspect outdated ports without changing the installation:

```console
./macportsupdate --check
```

Optional maintenance operations can be combined:

```console
./macportsupdate --cleanup-inactive
./macportsupdate --cleanup-saved
./macportsupdate --reclaim
./macportsupdate --cleanup-inactive --reclaim
```

Use `--yes` to allow noninteractive MacPorts operations and skip the saved-file confirmation prompt:

```console
./macportsupdate --yes
```

### `macportsupdate` options

| Option | Description |
| --- | --- |
| `--prefix PATH` | Use a MacPorts installation at an absolute prefix. |
| `--dry-run` | Preview mutating commands without executing them. |
| `--check` | Run read-only diagnostics and report outdated ports. |
| `--no-selfupdate` | Skip `port selfupdate`. |
| `--no-upgrade` | Skip upgrading outdated ports. |
| `--cleanup-inactive` | Uninstall inactive ports. |
| `--cleanup-saved` | Review and remove saved MacPorts configuration files. |
| `--reclaim` | Run `port reclaim`. |
| `--yes` | Allow noninteractive operations and confirm saved-file removal. |
| `--verbose` | Enable debug logging. |
| `--quiet` | Suppress informational console output. |
| `--log-file PATH` | Write logs to an absolute path. |
| `--no-log` | Disable file logging. |
| `--version` | Display the script version. |
| `-h`, `--help` | Display usage information. |

## Removing MacPorts

Preview the removal workflow:

```console
./macportsremove --dry-run
```

After reviewing the dry-run output, start the removal and confirm interactively:

```console
./macportsremove
```

For an unattended removal, use `--yes`:

```console
./macportsremove --yes
```

The removal workflow:

1. Validates the MacPorts executable.
2. Runs `port -fp uninstall installed`.
3. Removes the `macports` user and group when they exist.
4. Removes known MacPorts application, launch daemon, receipt, startup-item, and Tcl paths.
5. Prints a summary and returns a nonzero status if any stage fails.

### `macportsremove` options

| Option | Description |
| --- | --- |
| `--prefix PATH` | Use a MacPorts installation at an absolute prefix. |
| `--dry-run` | Preview all removal commands without executing them. |
| `--yes` | Skip the destructive-operation confirmation prompt. |
| `--verbose` | Enable debug logging. |
| `--quiet` | Suppress informational console output. |
| `--log-file PATH` | Write logs to an absolute path. |
| `--no-log` | Disable file logging. |
| `--version` | Display the script version. |
| `-h`, `--help` | Display usage information. |

## Logging and safety

By default, logs are written beneath:

```text
~/.local/state/macportsupdate/update.log
~/.local/state/macportsremove/remove.log
```

Use `--no-log` to disable logging or `--log-file PATH` to select another absolute path.

Both scripts also use a prefix-specific lock under `${TMPDIR:-/tmp}` to prevent concurrent runs. Failures are reported in the final summary and produce a nonzero exit status.

## MacPorts prefix detection

The scripts use this order:

1. The value supplied with `--prefix`.
2. A `port` executable found on `PATH`.
3. `/opt/local/bin/port`, the standard MacPorts location.

## Version

The current script version is `3.0.0`.
