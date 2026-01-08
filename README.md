# ezSWAYdisplay

A Python CLI tool for **Garuda Linux Sway Community Edition** that persists temporary display configurations to permanent config files.

## Problem Statement

When using `wdisplays` or `swaymsg` to configure monitors in Sway, changes are temporary and lost on reboot. This tool captures your current display layout and saves it permanently.

## Why This Tool Exists

Garuda Linux uses a modular Sway configuration architecture:
- `~/.config/sway/config` is managed by `garuda-sway-settings` and gets overwritten on updates
- `/etc/sway/` contains system defaults
- User customizations belong in `~/.config/sway/config.d/`

**ezSWAYdisplay** respects this architecture by writing to the correct location with proper load order.

## Features

✅ Queries live display state using `swaymsg -t get_outputs`  
✅ Generates proper Sway output configuration syntax  
✅ Writes to `~/.config/sway/config.d/99-display-layout.conf`  
✅ Creates automatic backups before overwriting  
✅ Optional Sway reload after saving  
✅ Dry-run mode to preview changes  
✅ Handles multiple monitors, scaling, positioning, and disabled outputs  

## Installation

1. Clone this repository:
```bash
git clone https://github.com/NModlin/ezSWAYdisplay.git
cd ezSWAYdisplay
```

2. Make the script executable:
```bash
chmod +x ezSWAYdisplay.py
```

3. (Optional) Create a symlink for easy access:
```bash
sudo ln -s $(pwd)/ezSWAYdisplay.py /usr/local/bin/ezSWAYdisplay
```

## Usage

### Basic Usage

1. Configure your displays using `wdisplays` or `swaymsg`
2. Run the tool to persist the configuration:
```bash
./ezSWAYdisplay.py
```

### Command-Line Options

```bash
./ezSWAYdisplay.py [OPTIONS]

Options:
  -n, --dry-run      Show what would be written without actually writing
  -R, --no-reload    Don't reload Sway after writing config
  -B, --no-backup    Don't create backup of existing config
  -h, --help         Show help message
```

### Examples

**Preview changes without writing:**
```bash
./ezSWAYdisplay.py --dry-run
```

**Save config without reloading Sway:**
```bash
./ezSWAYdisplay.py --no-reload
```

**Save without creating a backup:**
```bash
./ezSWAYdisplay.py --no-backup
```

## How It Works

1. **Query**: Runs `swaymsg -t get_outputs` to get current display configuration
2. **Parse**: Extracts resolution, refresh rate, position, scale, and enabled/disabled state
3. **Generate**: Creates Sway output configuration lines in proper syntax
4. **Backup**: Saves existing config with timestamp (unless `--no-backup`)
5. **Write**: Saves to `~/.config/sway/config.d/99-display-layout.conf`
6. **Reload**: Runs `swaymsg reload` to apply changes (unless `--no-reload`)

## Output Format

The generated config file looks like:
```
# Auto-generated display configuration for Sway
# Created by ezSWAYdisplay on 2026-01-08 14:30:00
# DO NOT EDIT MANUALLY - This file will be overwritten

output DP-1 mode 2560x1440@143.912Hz pos 0 0 scale 1.0
output HDMI-A-1 mode 1920x1080@60.000Hz pos 2560 0 scale 1.0
output eDP-1 disable
```

## Why `99-display-layout.conf`?

The `99-` prefix ensures this file loads **last** in lexicographical order, overriding any default output configurations from system files.

## Garuda-Specific Notes

- ✅ Safe for Garuda updates (doesn't touch system-managed files)
- ✅ Follows Garuda's modular config architecture
- ✅ Compatible with `garuda-sway-settings` package
- ✅ Works with swayfx (Garuda's Sway fork)

## Requirements

- Python 3.6+
- Sway/swayfx window manager
- `swaymsg` command (included with Sway)

## Troubleshooting

**"swaymsg not found"**  
Make sure you're running Sway and `swaymsg` is in your PATH.

**Config not persisting after reboot**  
Verify the file exists: `cat ~/.config/sway/config.d/99-display-layout.conf`

**Changes not applying**  
Run `swaymsg reload` manually or check Sway logs: `journalctl -u sway`

## License

MIT License - See LICENSE file for details

## Contributing

Issues and pull requests welcome! This tool is designed specifically for Garuda Linux Sway Edition but should work on any Sway setup.

## Author

Created for the Garuda Linux community.

