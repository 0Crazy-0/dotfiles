# Dotfiles - Arch Linux + Hyprland

Personal configuration for Arch Linux with Hyprland, based on [end-4/dots-hyprland](https://github.com/end-4/dots-hyprland). It ships with pre-configured settings and scripts for a fast, repeatable setup.

## Screenshots

| Widgets | Window management |
|:--|:--|
| ![Widgets](/Screenshots/SomeWidget.jpeg) | ![Window management](/Screenshots/WindowManagement.jpeg) |
| System controller | AI sidebar |
| ![System controller](/Screenshots/SystemController.jpeg) | ![AI sidebar](/Screenshots/IA.jpeg) |

## Why this fork?

The upstream project is excellent but its installation asks a lot of questions and offers many optional paths. This fork exists for a different trade-off:

- **Direct, fast installation.** Run one script and get a working desktop, without prompts for every optional component. The only questions left are truly optional (extra apps, wallpapers, reboot).
- **A simpler desktop.** Animations and extras from the original that got in the way were removed or toned down, especially around the Quickshell interface (settings panels, bar, background and wallpaper selector). The goal is a calm setup that stays out of the way.

If you want the full upstream experience with all its options, use [end-4/dots-hyprland](https://github.com/end-4/dots-hyprland) directly.

## Features

- Ready-to-use configuration, no manual per-app setup.
- Automated scripts for dependencies and installation.
- Included theme and color tooling (Matugen, Pywal).
- Safe installs: existing configs are backed up before being replaced.
- Two-way sync: pull updates from the repo, or push local changes back with `update.sh`.

## Differences from upstream

- Non-interactive-first install flow (`rice.sh` runs dependencies then configs).
- Simplified Quickshell UI (fewer animations and distractions).
- Wallpapers live in a separate repo and are cloned on demand.
- Extra tooling: protective `rm` wrapper, Fish + Starship defaults, Neovim config.

## Requirements

- Arch Linux or an Arch-based distribution.
- Internet connection to download packages.
- A user with sudo privileges.

## Installation

### 1. Clone the repository

```bash
git clone https://github.com/0CrazyLove/dotfiles ~/dotfiles
cd ~/dotfiles
```

### 2. Full installation (recommended)

```bash
./rice.sh
```

This runs `dependencies.sh` and then `install.sh`, with error handling between steps. After step 1 it pauses - press Enter to continue to step 2. At the end it offers to reboot, which is recommended.

### 3. Manual installation (alternative)

Run each script separately if you prefer more control:

```bash
./dependencies.sh
./install.sh
```

**`dependencies.sh`** prepares the system:

- Checks it runs on Arch (`pacman` must exist) and fixes `$HOME` ownership if needed.
- Verifies the pacman keyring and updates the system (`pacman -Syu`).
- Installs 75 official packages with pacman (full list in [Dependencies](#dependencies)).
- Installs `paru` if missing, then 37 AUR packages (full list in [Dependencies](#dependencies)).
- Offers 3 optional packages (VS Code, Discord, Brave) with a 30-second prompt; it skips them on timeout.
- Verifies critical packages (`hyprland`, `kitty`, `imagemagick`, `xdg-desktop-portal-hyprland`) at the end.

**`install.sh`** installs the configurations:

- Requires `~/dotfiles` to exist and `git`, `rsync`, `makepkg` to be available (it tells you to run `dependencies.sh` first if not).
- Clones upstream `end-4/dots-hyprland` to `~/dots-hyprland` if missing and builds `illogical-impulse-quickshell-git`, removing conflicting `quickshell` packages first.
- Optionally clones wallpapers from `https://github.com/0CrazyLove/walls` into `~/Documents/Walls` (asks once, skips on "no").
- Copies configs to their targets, backing up anything that already exists to `~/.configbackup/YYYYMMDD_HHMMSS/`. Installed items include Fish, Fastfetch, Hyprland, Kitty, Neovim, Quickshell, Illogical Impulse, Pywal (`wal` repo directory goes to `~/.config/wal`), Matugen, XDG portal config, icons and `starship.toml`. It also installs the protective `rm` wrapper to `/usr/local/bin/rm`.
- Sets up a Python virtual environment at `~/.local/state/quickshell/.venv` with `uv` using `requirements.txt`, applies the default wallpaper, and appends `starship init fish | source` to the Fish config if missing.

## Configuration management

Update the system from the repo:

```bash
cd ~/dotfiles
git pull
./install.sh
```

Send local changes back to the repo:

```bash
cd ~/dotfiles
./update.sh
```

`update.sh` rsyncs `~/.config/{fish,fastfetch,hypr,kitty,matugen,quickshell}` plus `starship.toml` into the repo, excluding machine-specific files (Fish variables, generated Hypr colors, lock-screen colors and similar). It then shows `git status` and optionally commits and pushes.

## Dependencies

Source of truth: the `OFFICIAL_PACKAGES`, `AUR_PACKAGES` and `OPTIONAL_PACKAGES` arrays in `dependencies.sh`.

### Official packages (pacman, 75)

```text
base-devel fish starship hyprland kitty neovim qt5-tools dolphin
ffmpegthumbs eza cliphist ddcutil fuzzel glib2 hypridle hyprutils
hyprlock hyprpicker nm-connection-editor swww grim slurp geoclue nano
brightnessctl axel bc coreutils cmake curl rsync wget ripgrep jq meson
xdg-user-dirs fontconfig breeze tinyxml2 gtkmm3 cairomm gtk4 libadwaita
libsoup3 gobject-introspection sassc tesseract tesseract-data-eng
wf-recorder kdialog less qt6-base qt6-declarative qt6-imageformats
qt6-multimedia qt6-positioning qt6-quicktimeline qt6-sensors qt6-svg
qt6-tools qt6-translations qt6-wayland upower qt6-5compat
syntax-highlighting imagemagick python-pip go cava gnome-system-monitor
pavucontrol-qt fastfetch songrec hyprsunset noto-fonts-cjk
```

### AUR packages (paru, 37)

```text
translate-shell python-materialyoucolor wlogout adw-gtk-theme-git
breeze-plus darkly-bin matugen-bin otf-space-grotesk ttf-gabarito-git
ttf-jetbrains-mono-nerd ttf-material-symbols-variable-git ttf-readex-pro
ttf-rubik-vf ttf-twemoji hyprcursor hyprland-qt-support hyprlang
hyprwayland-scanner xdg-desktop-portal-hyprland wl-clipboard bluedevil
gnome-keyring networkmanager plasma-nm polkit-kde-agent systemsettings uv
hyprshot swappy wtype ydotool google-breakpad qt6-avif-image-plugin
upscayl-bin cpptrace jemalloc
```

### Optional packages (paru, prompted, 3)

```text
visual-studio-code-bin discord brave-bin
```

## Configured applications

| Area | Applications |
|:--|:--|
| Shell and prompt | Fish, Starship |
| Compositor and terminal | Hyprland, Kitty |
| App shell and theming | Quickshell (Illogical Impulse), Matugen, Pywal |
| Launcher and screenshots | Fuzzel, Grim + Slurp, Hyprshot, Swappy |
| Clipboard and tools | Cliphist, `wl-clipboard`, Tesseract (OCR) |
| System info and media | Fastfetch, Pavucontrol-Qt, GNOME System Monitor, Cava, Songrec |
| Editors and files | Neovim, Dolphin |
| Fonts | JetBrainsMono Nerd, Space Grotesk, Gabarito, Readex Pro, Rubik, Twemoji |

Launchers resolve the first available app (for example the browser tries Zen, Firefox, Brave, Chromium, Edge, Opera, then Librewolf), so missing optional apps fall back gracefully.

## Keyboard shortcuts

All shortcuts use **Super** (Windows key) as the main modifier. The full list lives in `.config/hypr/hyprland/keybinds.lua` - press `Super + /` anytime to open the on-screen cheatsheet.

### Workspaces

| Shortcut | Action |
|:--|:--|
| `Super + 1-0` | Focus workspace 1-10 |
| `Super + Alt + 1-0` | Move window to workspace 1-10 |
| `Super + S` | Toggle scratchpad |

### Windows

| Shortcut | Action |
|:--|:--|
| `Super + Q` | Close window |
| `Super + F` | Fullscreen |
| `Super + Alt + F` | Fake fullscreen |
| `Super + D` | Maximize |
| `Super + P` | Pin window |
| `Super + V` | Toggle floating (Hyprland fallback; with Quickshell running it opens clipboard history) |
| `Super + L` | Lock session |

### Applications

| Shortcut | Action |
|:--|:--|
| `Super + Enter` | Terminal (Kitty, via first-available launcher) |
| `Super + E` | File manager (Dolphin) |
| `Super + W` | Browser |
| `Super + C` | Code editor |
| `Super + X` | Text editor |
| `Super + I` | Settings |
| `Ctrl + Shift + Esc` | Task manager |
| `Ctrl + Super + V` | Volume mixer |

### Quickshell and sidebars

| Shortcut | Action |
|:--|:--|
| `Super + O` / `Super + A` / `Super + B` | Left sidebar (includes AI assistant) |
| `Super + N` | Right sidebar |
| `Super + J` | Toggle bar |
| `Super + G` | Widget overlay |
| `Super + Tab` | Workspaces overview |
| `Super + /` | Cheatsheet |
| `Ctrl + Super + R` | Restart widgets |

### Screenshots, recording and tools

| Shortcut | Action |
|:--|:--|
| `Super + Shift + S` | Region screenshot |
| `Super + Shift + T` | OCR selected region to clipboard |
| `Super + Shift + X` | Translate screen content |
| `Super + Shift + A` | Visual search (Google Lens) |
| `Super + Shift + C` | Color picker to clipboard |
| `Super + Shift + R` / `Super + Alt + R` | Record region (no audio) |
| `Ctrl + Alt + R` | Record fullscreen (no audio) |
| `Super + Shift + Alt + R` | Record fullscreen (with audio) |
| `Print` | Screenshot monitor to clipboard |
| `Ctrl + Print` | Screenshot to file and clipboard |
| `Super + V` | Clipboard history |
| `Super + .` | Emoji picker |

Recordings are saved to `~/Videos/`. Run the same record shortcut again to stop.

### Audio and media

| Shortcut | Action |
|:--|:--|
| `Super + Shift + M` | Mute/unmute speakers |
| `Super + Alt + M` | Mute/unmute microphone |
| `Super + Shift + N` / `B` / `P` | Next / previous / play-pause (Spotify-oriented) |

### Zoom and display

| Shortcut | Action |
|:--|:--|
| `Super + =` | Zoom in at cursor |
| `Super + -` | Zoom out |
| `Ctrl + Super + T` | Wallpaper selector |
| `Ctrl + Super + Alt + T` | Random wallpaper |

## AI assistant

Open it with `Super + O`, `Super + A` or `Super + B`, or right-click the top-left of the bar.

Set your API key (from <https://aistudio.google.com/app/apikey>) inside the assistant's Intelligence section:

```text
/key YOUR_GOOGLE_API_KEY
```

## Customization

Wallpapers (cloned on demand):

```bash
ls ~/Documents/Walls/
```

Edit a config and sync it back:

```bash
# edit files under ~/.config/, then:
cd ~/dotfiles
./update.sh
```

## Project structure

```text
dotfiles/
├── .config/
│   ├── fish/                  # Fish shell
│   ├── fastfetch/             # System info
│   ├── hypr/                  # Hyprland (hyprland/ + custom/ overrides)
│   ├── kitty/                 # Terminal
│   ├── nvim/                  # Neovim
│   ├── quickshell/            # Quickshell (ii) interface
│   ├── illogical-impulse/     # Theme data
│   ├── matugen/               # Color scheme templates
│   ├── xdg-desktop-portal/    # Portal config
│   └── starship.toml          # Prompt
├── .local/share/icons/        # Bundled icons
├── bin/rm                     # Protective rm wrapper (installed to /usr/local/bin/rm)
├── wal/                       # Pywal schemes (installed to ~/.config/wal)
├── pkgs/                      # Local packaging files
├── dependencies.sh            # System dependencies
├── install.sh                 # Config installation
├── rice.sh                    # Full install (dependencies + install)
├── update.sh                  # Sync local configs back to the repo
└── requirements.txt           # Python venv packages for Quickshell
```

Wallpapers are not stored here; they come from the separate `walls` repo into `~/Documents/Walls/`.

## Troubleshooting

**Pacman database lock** (`unable to lock database` after an interrupted install):

```bash
ps aux | grep pacman
# if nothing is running:
sudo rm /var/lib/pacman/db.lck
./dependencies.sh
```

**Permission errors:**

```bash
sudo chown -R "$USER:$USER" "$HOME"
chmod 755 "$HOME"
```

The scripts check and fix this automatically on each run.

**Missing dependencies:**

```bash
./dependencies.sh
```

**Fish is not the default shell:**

```bash
chsh -s /usr/bin/fish
# then start a new session
```

**Restore a backup:**

```bash
ls ~/.configbackup/
cp -r ~/.configbackup/YYYYMMDD_HHMMSS/<item> ~/.config/
```

## Credits

- [end-4/dots-hyprland](https://github.com/end-4/dots-hyprland) - upstream base and most of the desktop logic.
- Hyprland community and Arch Linux for the underlying platform.

## License

GPL-3.0 - see [LICENSE](LICENSE). This follows the upstream open-source licensing.

## Contact

Questions or install issues: reach out on Discord at `0crazylove0`.
