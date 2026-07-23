# Kitty Terminal Configuration

A minimal, well-structured kitty terminal configuration optimized for Neovim development on Linux (Fedora).

---

## Table of Contents

- [Getting Started](#getting-started)
  - [Prerequisites](#prerequisites)
  - [Installation](#installation)
  - [First Launch Checklist](#first-launch-checklist)
- [Usage Guide](#usage-guide)
  - [Daily Workflow](#daily-workflow)
  - [Working with Neovim Inside Kitty](#working-with-neovim-inside-kitty)
  - [Using Kitty as a Multiplexer (Replacing tmux)](#using-kitty-as-a-multiplexer-replacing-tmux)
  - [Mouse Usage](#mouse-usage)
  - [URL Handling](#url-handling)
  - [Scrollback and History](#scrollback-and-history)
  - [Hints Mode (Smart Selection)](#hints-mode-smart-selection)
  - [Remote Control and Scripting](#remote-control-and-scripting)
  - [SSH with Kitty](#ssh-with-kitty)
  - [Kitty Kittens (Built-in Tools)](#kitty-kittens-built-in-tools)
  - [Broadcasting Input](#broadcasting-input)
- [Complete Key Command Reference](#complete-key-command-reference)
  - [Clipboard](#clipboard-keys)
  - [Scrolling](#scrolling-keys)
  - [Window (Split) Management](#window-split-management-keys)
  - [Tab Management](#tab-management-keys)
  - [Layout Control](#layout-control-keys)
  - [Font Size](#font-size-keys)
  - [Selection and Marks](#selection-and-marks-keys)
  - [Miscellaneous](#miscellaneous-keys)
- [Configuration Guidelines](#configuration-guidelines)
  - [Config File Syntax](#config-file-syntax)
  - [How to Add a New Keybinding](#how-to-add-a-new-keybinding)
  - [How to Change a Setting](#how-to-change-a-setting)
  - [How to Override or Disable a Default Keybinding](#how-to-override-or-disable-a-default-keybinding)
  - [Using Environment Variables in Config](#using-environment-variables-in-config)
  - [Conditional Configuration (OS/Platform)](#conditional-configuration-osplatform)
  - [Including External Config Files](#including-external-config-files)
  - [Setting Environment Variables for Child Processes](#setting-environment-variables-for-child-processes)
  - [Tab Bar Customization](#tab-bar-customization)
  - [Background Opacity and Blur](#background-opacity-and-blur)
  - [Config Validation and Debugging](#config-validation-and-debugging)
  - [Startup Sessions](#startup-sessions)
- [File Structure](#file-structure)
- [Configuration Breakdown](#configuration-breakdown)
  - [Theme](#theme)
  - [Fonts](#fonts)
  - [Clipboard and Selection](#clipboard-and-selection)
  - [Keyboard Shortcuts](#keyboard-shortcuts)
  - [Neovim Integration](#neovim-integration)
  - [Scrollback](#scrollback)
  - [Window and Layout](#window-and-layout)
  - [Performance](#performance)
- [Neovim Setup](#neovim-setup)
  - [Required Neovim Settings](#required-neovim-settings)
  - [Recommended Neovim Plugins](#recommended-neovim-plugins)
- [Common Operations](#common-operations)
  - [Copy and Paste](#copy-and-paste)
  - [Font Size](#font-size)
  - [Window Management](#window-management)
  - [Tabs](#tabs)
  - [Reloading Configuration](#reloading-configuration)
- [Changing the Theme](#changing-the-theme)
- [Troubleshooting](#troubleshooting)
  - [Cannot Copy Text](#cannot-copy-text)
  - [Undercurl Not Rendering in Neovim](#undercurl-not-rendering-in-neovim)
  - [Neovim Colors Look Wrong](#neovim-colors-look-wrong)
  - [Kitty Remote Control Not Working](#kitty-remote-control-not-working)
  - [Font Not Found](#font-not-found)
  - [SSH Breaks with xterm-kitty](#ssh-breaks-with-xterm-kitty)
  - [Keyboard Input Not Recognized by Programs](#keyboard-input-not-recognized-by-programs)
  - [Splits or Windows Not Working as Expected](#splits-or-windows-not-working-as-expected)
  - [Performance Issues (Lag, High CPU)](#performance-issues-lag-high-cpu)

---

## Getting Started

### Prerequisites

- **Kitty terminal** installed (`sudo dnf install kitty` on Fedora)
- **Neovim** 0.9+ recommended (`sudo dnf install neovim`)
- **A Nerd Font** (optional but recommended for icons in nvim plugins)
- **xclip or wl-clipboard** for system clipboard integration:
  ```bash
  # X11
  sudo dnf install xclip xsel

  # Wayland
  sudo dnf install wl-clipboard
  ```

### Installation

1. Back up any existing config:
   ```bash
   cp ~/.config/kitty/kitty.conf ~/.config/kitty/kitty.conf.bak
   ```

2. Place `kitty.conf` and `current-theme.conf` in `~/.config/kitty/`.

3. Reload kitty:
   - Press `Ctrl+Shift+F5` inside kitty, **or**
   - Close and reopen kitty

4. Verify the config loaded correctly:
   ```bash
   kitty +kitten show_config
   ```

### First Launch Checklist

After installing, verify these work:

- [ ] **Copy works:** Select text with mouse, then paste into another app with `Ctrl+V`
- [ ] **Theme loaded:** Background should be dark purple-black (`#201d2a`)
- [ ] **Font size:** Text should be readable at 12pt. Adjust with `Ctrl+Shift+=`/`-`
- [ ] **Nvim colors:** Open nvim — colors should match your theme (requires `termguicolors = true`)
- [ ] **Splits work:** Press `Ctrl+Shift+Enter` to create a split, `Ctrl+Shift+W` to close it
- [ ] **Remote control:** Run `kitty @ ls` — should output JSON about your kitty windows

---

## Usage Guide

### Daily Workflow

A typical development session using kitty + nvim:

1. **Open kitty** — launches with your shell in a single window
2. **Open nvim** — edit files as normal, clipboard is shared with kitty
3. **Split the terminal** — `Ctrl+Shift+Enter` to open a shell beside nvim for running commands
4. **Switch between splits** — `Ctrl+Shift+]` / `Ctrl+Shift+[`
5. **Use tabs** for separate projects — `Ctrl+Shift+T` for new tab, `Ctrl+Shift+Right/Left` to switch
6. **Stack layout** — `Ctrl+Shift+L` to toggle fullscreen on current split (focus mode)
7. **Copy anything** — select text with mouse (auto-copies) or use `Ctrl+Shift+C`

### Working with Neovim Inside Kitty

**Clipboard flow:**

```
nvim yank (y) --> system clipboard --> Ctrl+V in browser/other apps
browser copy (Ctrl+C) --> system clipboard --> nvim paste (p)
kitty select text --> system clipboard --> nvim paste (p) or Ctrl+V anywhere
```

This works because:
- Kitty's `clipboard_control` allows programs to access the system clipboard
- Nvim's `clipboard = "unnamedplus"` routes yank/paste through the system clipboard
- Kitty's `copy_on_select clipboard` auto-copies mouse selections

**Navigating between nvim splits and kitty splits:**

Without a plugin, nvim splits and kitty splits are separate — `Ctrl+W+h/j/k/l` moves within nvim, `Ctrl+Shift+]/[` moves within kitty. To unify them, install `vim-kitty-navigator` or `smart-splits.nvim` (see [Recommended Neovim Plugins](#recommended-neovim-plugins)).

**Opening files from kitty in nvim:**

```bash
# Open a file in a running nvim instance (requires nvim --listen)
nvim --server /tmp/nvim.sock --remote file.txt

# Or start nvim listening on a socket
nvim --listen /tmp/nvim.sock
```

**Using kitty's scrollback in nvim:**

Press `Ctrl+Shift+H` to view scrollback in your pager. For a richer experience, the `kitty-scrollback.nvim` plugin opens scrollback directly in nvim with full search, yank, and syntax highlighting.

### Using Kitty as a Multiplexer (Replacing tmux)

Kitty has built-in window management that can replace tmux for most workflows:

| tmux Concept | Kitty Equivalent | Key |
|-------------|------------------|-----|
| Pane | Window (split) | `Ctrl+Shift+Enter` |
| Window | Tab | `Ctrl+Shift+T` |
| Session | OS Window | `Ctrl+Shift+N` (new OS window) |
| Horizontal split | `kitty @ launch --location=hsplit` | Configurable |
| Vertical split | `kitty @ launch --location=vsplit` | Configurable |
| Resize pane | `Ctrl+Shift+R` then arrow keys | Enter resize mode |
| Detach/reattach | Not built-in | Use startup sessions instead |

**Adding tmux-style split shortcuts** (optional, add to `kitty.conf`):

```conf
# Ctrl+Shift+\ for vertical split, Ctrl+Shift+- for horizontal
map ctrl+shift+\ launch --location=vsplit --cwd=current
map ctrl+shift+- launch --location=hsplit --cwd=current
```

**Kitty's advantage over tmux:**
- Native GPU rendering (no redraw lag)
- True color and undercurl work without workarounds
- Image display support (kitty graphics protocol)
- No nested escape sequence issues

**What tmux still does better:**
- Session persistence (survives terminal close)
- Remote server attach/detach

### Mouse Usage

| Action | Result |
|--------|--------|
| **Click** | Place cursor |
| **Click on URL** | Open in browser (when holding `Ctrl` or based on `open_url_with` setting) |
| **Select text** (click and drag) | Auto-copies to clipboard (with `copy_on_select clipboard`) |
| **Double-click** | Select word |
| **Triple-click** | Select line |
| **Right-click** | Extend selection |
| **Middle-click** | Paste from primary selection |
| **Scroll wheel** | Scroll output history |
| **Ctrl+Scroll wheel** | Change font size |

### URL Handling

Kitty detects URLs in terminal output and underlines them on hover.

- **Click a URL** while holding `Ctrl` to open it in your default browser
- The URL color is set by your theme (currently `#dcd2fe`)
- Configure the opener:
  ```conf
  open_url_with default
  ```
  Change `default` to a specific browser like `firefox` or `google-chrome`.

**Selecting URLs without the mouse:**

```
Ctrl+Shift+E
```

This activates **hints mode** for URLs — each detected URL gets a short label. Type the label to open it. Very useful for keyboard-only workflows.

### Scrollback and History

| Action | Method |
|--------|--------|
| Scroll up/down | `Ctrl+Shift+Up/Down` or mouse wheel |
| Scroll page up/down | `Ctrl+Shift+Page_Up/Page_Down` |
| Scroll to top/bottom | `Ctrl+Shift+Home/End` |
| Browse full scrollback in pager | `Ctrl+Shift+H` |
| Browse scrollback in nvim | `kitty +kitten show_scrollback` (or `kitty-scrollback.nvim` plugin) |
| Search scrollback | Inside pager (`/` in less) |
| Last command output | `Ctrl+Shift+G` (with shell integration) |

**Shell integration features** (enabled by `shell_integration enabled`):

- Each command prompt is marked — you can jump between prompts
- `Ctrl+Shift+Z` — scroll to previous command prompt
- `Ctrl+Shift+X` — scroll to next command prompt
- `Ctrl+Shift+G` — view just the output of the last command

### Hints Mode (Smart Selection)

Hints mode lets you select specific types of text using the keyboard:

| Shortcut | What it selects |
|----------|----------------|
| `Ctrl+Shift+E` | URLs — type label to open |
| `Ctrl+Shift+P > F` | File paths — type label to open in `$EDITOR` |
| `Ctrl+Shift+P > H` | Hash-like strings (git SHAs, etc.) |
| `Ctrl+Shift+P > N` | Line numbers from compiler output |
| `Ctrl+Shift+P > W` | Words |
| `Ctrl+Shift+P > Y` | Hyperlinks |

These are extremely useful for:
- Opening URLs without touching the mouse
- Copying git commit hashes
- Jumping to file:line from error output

### Remote Control and Scripting

With `allow_remote_control yes` and `listen_on unix:/tmp/kitty`, you can script kitty from the command line or from nvim:

```bash
# List all kitty windows
kitty @ ls

# Set the current tab title
kitty @ set-tab-title "My Project"

# Change the background color on the fly
kitty @ set-colors background=#1a1b26

# Launch a new window with a command
kitty @ launch --title "Server" python -m http.server

# Send text to a specific window
kitty @ send-text --match title:Server "hello\n"

# Create a new tab and run a command
kitty @ launch --type=tab --tab-title="Logs" tail -f /var/log/syslog

# Close a specific window
kitty @ close-window --match title:Server

# Focus a specific tab
kitty @ focus-tab --match title:Logs

# Get text from the current window's scrollback
kitty @ get-text --extent=all
```

**Use in shell scripts:**

```bash
#!/bin/bash
# Example: development environment launcher
kitty @ set-tab-title "Editor"
kitty @ launch --type=tab --tab-title="Server" npm run dev
kitty @ launch --type=tab --tab-title="Tests" npm run test:watch
kitty @ focus-tab --match title:Editor
```

**Use in nvim (Lua):**

```lua
-- Send a command to a kitty window from nvim
vim.fn.system("kitty @ launch --type=tab --tab-title='Terminal' zsh")
```

### SSH with Kitty

When SSHing from kitty, the remote server may not have the `xterm-kitty` terminfo. This causes display issues.

**Option 1: Use kitty's SSH kitten (recommended):**

```bash
kitty +kitten ssh user@host
```

This automatically copies the terminfo to the remote server and sets up the shell integration.

**Option 2: Manually copy terminfo:**

```bash
infocmp -a xterm-kitty | ssh user@host tic -x -o ~/.terminfo /dev/stdin
```

**Option 3: Fall back to a standard TERM on remote:**

Add to your `~/.bashrc` or `~/.zshrc` on the remote server:

```bash
if [ "$TERM" = "xterm-kitty" ] && ! infocmp xterm-kitty &>/dev/null; then
    export TERM=xterm-256color
fi
```

### Kitty Kittens (Built-in Tools)

Kittens are built-in helper programs that extend kitty's functionality:

| Kitten | Command | Purpose |
|--------|---------|---------|
| **themes** | `kitty +kitten themes` | Interactive theme browser/switcher |
| **choose-fonts** | `kitty +kitten choose-fonts` | Interactive font browser with previews |
| **diff** | `kitty +kitten diff file1 file2` | Side-by-side file diff with syntax highlighting |
| **icat** | `kitty +kitten icat image.png` | Display images directly in the terminal |
| **ssh** | `kitty +kitten ssh user@host` | SSH with automatic terminfo and shell integration |
| **hints** | `Ctrl+Shift+E` | Select URLs, paths, hashes from screen |
| **unicode_input** | `Ctrl+Shift+U` | Insert Unicode characters by name |
| **clipboard** | `kitty +kitten clipboard` | Read/write system clipboard from scripts |
| **show_key** | `kitty +kitten show_key` | Show key press escape codes (useful for debugging) |
| **show_config** | `kitty +kitten show_config` | Dump current resolved configuration |
| **broadcast** | `Ctrl+Shift+A` | Type in all windows simultaneously |

### Broadcasting Input

To type the same input in all visible windows simultaneously:

```
Ctrl+Shift+A
```

This toggles broadcast mode. Everything you type goes to all windows in the current tab. Press `Ctrl+Shift+A` again to stop.

Useful for:
- Running the same command on multiple servers
- Setting up identical environments in multiple splits

---

## Complete Key Command Reference

All keyboard shortcuts available in this configuration. Custom shortcuts (defined in `kitty.conf`) are marked with **(custom)**. All others are kitty built-in defaults.

### Clipboard {#clipboard-keys}

| Shortcut | Action | Notes |
|----------|--------|-------|
| `Ctrl+Shift+C` | Copy to clipboard | **(custom)** Copies selected text |
| `Ctrl+Shift+V` | Paste from clipboard | **(custom)** |
| **Mouse select** | Auto-copy to clipboard | Via `copy_on_select clipboard` |
| **Middle-click** | Paste primary selection | Standard X11/Wayland behavior |
| `Ctrl+Shift+S` | Paste from selection | Pastes primary selection via keyboard |
| `Ctrl+Shift+O` | Pass selection to program | Pipes selection to a configured program |

### Scrolling {#scrolling-keys}

| Shortcut | Action |
|----------|--------|
| `Ctrl+Shift+Up` | Scroll up one line |
| `Ctrl+Shift+Down` | Scroll down one line |
| `Ctrl+Shift+Page_Up` | Scroll up one page |
| `Ctrl+Shift+Page_Down` | Scroll down one page |
| `Ctrl+Shift+Home` | Scroll to top of scrollback |
| `Ctrl+Shift+End` | Scroll to bottom (most recent output) |
| `Ctrl+Shift+H` | Browse scrollback in pager (`less`) |
| `Ctrl+Shift+G` | View output of last command (shell integration) |
| `Ctrl+Shift+Z` | Scroll to previous shell prompt (shell integration) |
| `Ctrl+Shift+X` | Scroll to next shell prompt (shell integration) |

### Window (Split) Management {#window-split-management-keys}

| Shortcut | Action |
|----------|--------|
| `Ctrl+Shift+Enter` | New window (split) in current tab |
| `Ctrl+Shift+W` | Close current window |
| `Ctrl+Shift+]` | Focus next window |
| `Ctrl+Shift+[` | Focus previous window |
| `Ctrl+Shift+F` | Move current window forward in layout |
| `Ctrl+Shift+B` | Move current window backward in layout |
| `Ctrl+Shift+R` | Enter resize mode (then use arrow keys, `Esc` to exit) |
| `Ctrl+Shift+1-9` | Focus window by number (1=first, etc.) |
| `Ctrl+Shift+F7` | Focus visually previous window |
| `Ctrl+Shift+F8` | Focus visually next window |
| `Ctrl+Shift+`\` ` | Move window to top |
| `Ctrl+Shift+N` | New OS window (separate kitty window) |

**Resize mode** (`Ctrl+Shift+R`):

| Key (in resize mode) | Action |
|-----------------------|--------|
| `Left` / `Right` | Make window narrower / wider |
| `Up` / `Down` | Make window shorter / taller |
| `Esc` or `Enter` | Exit resize mode |
| `R` | Reset all windows to equal size |

### Tab Management {#tab-management-keys}

| Shortcut | Action |
|----------|--------|
| `Ctrl+Shift+T` | New tab |
| `Ctrl+Shift+Q` | Close tab |
| `Ctrl+Shift+Right` | Next tab |
| `Ctrl+Shift+Left` | Previous tab |
| `Ctrl+Shift+.` | Move tab forward |
| `Ctrl+Shift+,` | Move tab backward |
| `Ctrl+Shift+Alt+T` | Set tab title |
| `Ctrl+Shift+Alt+1-9` | Go to tab by number |

### Layout Control {#layout-control-keys}

| Shortcut | Action |
|----------|--------|
| `Ctrl+Shift+L` | Cycle through enabled layouts |

**Enabled layouts in this config:**

| Layout | Behavior |
|--------|----------|
| `splits` | Free-form horizontal/vertical splits (default) |
| `stack` | Single window fullscreen, others hidden |

Press `Ctrl+Shift+L` to toggle. In `stack` mode, use `Ctrl+Shift+]/[` to switch which window is shown.

### Font Size {#font-size-keys}

| Shortcut | Action |
|----------|--------|
| `Ctrl+Shift+=` | Increase font size by 1pt | **(custom)** |
| `Ctrl+Shift+-` | Decrease font size by 1pt | **(custom)** |
| `Ctrl+Shift+Backspace` | Reset to default (12pt) | **(custom)** |
| `Ctrl+Scroll Up` | Increase font size (mouse) |
| `Ctrl+Scroll Down` | Decrease font size (mouse) |

### Selection and Marks {#selection-and-marks-keys}

| Shortcut | Action |
|----------|--------|
| `Ctrl+Shift+E` | Open URL hints (type label to open) |
| `Ctrl+Shift+P > F` | Select file path from screen |
| `Ctrl+Shift+P > H` | Select hash from screen |
| `Ctrl+Shift+P > N` | Select line number from screen |
| `Ctrl+Shift+P > W` | Select word from screen |
| `Ctrl+Shift+P > Y` | Select hyperlink from screen |
| `Ctrl+Shift+U` | Unicode input (search by character name) |
| `Ctrl+Shift+A` | Toggle broadcast mode (type in all windows) |

### Miscellaneous {#miscellaneous-keys}

| Shortcut | Action |
|----------|--------|
| `Ctrl+Shift+F5` | Reload `kitty.conf` (apply changes without restart) |
| `Ctrl+Shift+F2` | Open `kitty.conf` in `$EDITOR` |
| `Ctrl+Shift+F6` | Show kitty debug info |
| `Ctrl+Shift+Delete` | Clear terminal screen + scrollback |
| `Ctrl+Shift+F11` | Toggle fullscreen |
| `Ctrl+Shift+F10` | Toggle maximized |
| `Ctrl+Shift+Escape` | Open kitty shell (interactive `kitty @` REPL) |

---

## Configuration Guidelines

### Config File Syntax

`kitty.conf` uses a simple `key value` format:

```conf
# Comments start with #
setting_name value

# Strings don't need quotes
font_family JetBrains Mono

# Numbers
font_size 12.0

# Booleans: yes/no
allow_remote_control yes

# Colors: hex
background #201d2a

# Keybindings: map <shortcut> <action>
map ctrl+shift+c copy_to_clipboard

# Multi-value settings: space-separated
clipboard_control write-clipboard write-primary read-clipboard read-primary
```

**Important rules:**
- One setting per line
- No `=` sign — just space between key and value
- Lines starting with `#` are comments
- Settings are applied top-to-bottom; last value wins if duplicated
- Use `include` to import other files
- Vim foldmarkers (`{{{`/`}}}`) are used for section folding in this config

### How to Add a New Keybinding

```conf
# Syntax
map <modifier+key> <action> [arguments]

# Examples
map ctrl+shift+t new_tab
map ctrl+shift+enter new_window
map ctrl+alt+l next_layout
map f1 launch --type=tab htop
map ctrl+shift+\ launch --location=vsplit --cwd=current
```

**Available modifiers:** `ctrl`, `shift`, `alt`, `super` (Windows/Meta key). Combine with `+`.

**Common actions for keybindings:**

| Action | Description | Example |
|--------|-------------|---------|
| `copy_to_clipboard` | Copy selection to clipboard | `map ctrl+shift+c copy_to_clipboard` |
| `paste_from_clipboard` | Paste from clipboard | `map ctrl+shift+v paste_from_clipboard` |
| `new_tab` | Create a new tab | `map ctrl+shift+t new_tab` |
| `close_tab` | Close current tab | `map ctrl+shift+q close_tab` |
| `new_window` | Create new window/split | `map ctrl+shift+enter new_window` |
| `close_window` | Close current window | `map ctrl+shift+w close_window` |
| `next_window` | Focus next window | `map ctrl+shift+] next_window` |
| `previous_window` | Focus previous window | `map ctrl+shift+[ previous_window` |
| `next_tab` | Focus next tab | `map ctrl+shift+right next_tab` |
| `previous_tab` | Focus previous tab | `map ctrl+shift+left previous_tab` |
| `next_layout` | Cycle layouts | `map ctrl+shift+l next_layout` |
| `change_font_size` | Change font size | `map ctrl+shift+equal change_font_size all +1.0` |
| `launch` | Launch a program | `map f1 launch --type=tab htop` |
| `scroll_line_up` | Scroll up one line | `map ctrl+shift+up scroll_line_up` |
| `scroll_line_down` | Scroll down one line | `map ctrl+shift+down scroll_line_down` |
| `show_scrollback` | Open scrollback in pager | `map ctrl+shift+h show_scrollback` |
| `set_tab_title` | Rename current tab | `map ctrl+shift+alt+t set_tab_title` |
| `toggle_fullscreen` | Toggle fullscreen | `map ctrl+shift+f11 toggle_fullscreen` |
| `kitten` | Run a kitten | `map ctrl+shift+e kitten hints` |
| `send_text` | Send literal text to terminal | `map ctrl+alt+g send_text all git status\n` |

### How to Change a Setting

1. Open `kitty.conf`:
   ```
   Ctrl+Shift+F2     (inside kitty)
   ```
   Or manually:
   ```bash
   nvim ~/.config/kitty/kitty.conf
   ```

2. Find or add the setting. If the setting already exists, change its value. If it doesn't exist, add it anywhere (ideally in the relevant section).

3. Save the file and reload:
   ```
   Ctrl+Shift+F5
   ```

4. Verify with:
   ```bash
   kitty +kitten show_config | grep <setting_name>
   ```

### How to Override or Disable a Default Keybinding

**Override:** Simply map the same shortcut to a new action. Your config takes priority over defaults.

```conf
# Override Ctrl+Shift+T to open a tab with a specific directory
map ctrl+shift+t launch --type=tab --cwd=~/projects
```

**Disable:** Map the shortcut to `no_op` (no operation) or `discard_event`:

```conf
# Disable Ctrl+Shift+Q (close tab) to prevent accidental closes
map ctrl+shift+q no_op

# discard_event prevents the key from being sent to the running program too
map ctrl+shift+q discard_event
```

**Remove all default shortcuts and start from scratch:**

```conf
clear_all_shortcuts yes
# Now define only the shortcuts you want
map ctrl+shift+c copy_to_clipboard
map ctrl+shift+v paste_from_clipboard
# ... etc
```

### Using Environment Variables in Config

You can reference environment variables with `${VARIABLE}` or `$VARIABLE`:

```conf
# Use an environment variable for the font
font_family $KITTY_FONT

# Conditional socket path
listen_on unix:/tmp/kitty-${USER}
```

Set the variable in your shell profile (`~/.zshrc`):

```bash
export KITTY_FONT="JetBrains Mono"
```

### Conditional Configuration (OS/Platform)

Kitty does not have a built-in `if/else` in its config syntax, but you can use `include` with separate files:

```conf
# In kitty.conf - include platform-specific overrides
include kitty-${KITTY_OS}.conf
```

Then create:
- `kitty-linux.conf` — Linux-specific settings
- `kitty-macos.conf` — macOS-specific settings

If the file doesn't exist, the include is silently ignored.

Alternatively, use `env` to detect at runtime:

```conf
# Only applied on Wayland
# (must be set before kitty starts, e.g., in shell profile)
# export KITTY_WAYLAND=1
```

### Including External Config Files

```conf
# Include another file (relative to kitty.conf location)
include current-theme.conf

# Include with absolute path
include /home/user/.config/kitty/overrides.conf

# Include a glob pattern
include themes/*.conf

# If the file doesn't exist, it's silently ignored
include optional-config.conf
```

Use this to:
- Separate theme from config (already done)
- Keep machine-specific overrides in a separate file
- Share a base config across machines

### Setting Environment Variables for Child Processes

Set environment variables that will be available in all shells/programs launched by kitty:

```conf
# Set env vars for all child processes
env TERM_PROGRAM=kitty
env EDITOR=nvim
env GIT_EDITOR=nvim

# Unset a variable
env UNWANTED_VAR=
```

### Tab Bar Customization

```conf
# Tab bar position: top or bottom
tab_bar_edge bottom

# Tab bar style: fade, slant, separator, powerline, custom, hidden
tab_bar_style powerline

# Powerline style: angled, slanted, round
tab_powerline_style slanted

# Tab title template (Python format string)
tab_title_template "{index}: {title}"

# Active tab is bold
active_tab_font_style bold
inactive_tab_font_style normal

# Minimum number of tabs before bar is shown
tab_bar_min_tabs 2
```

**Tab title template variables:**

| Variable | Description |
|----------|-------------|
| `{index}` | Tab number (1-based) |
| `{title}` | Tab title (usually the running command) |
| `{layout_name}` | Current layout |
| `{num_windows}` | Number of windows in tab |
| `{num_window_groups}` | Number of window groups |

### Background Opacity and Blur

```conf
# Make the background semi-transparent (0.0 = fully transparent, 1.0 = opaque)
background_opacity 0.95

# Dim the background of unfocused windows
dim_opacity 0.9

# Dynamic background opacity keybindings
map ctrl+shift+a>m set_background_opacity +0.1
map ctrl+shift+a>l set_background_opacity -0.1
map ctrl+shift+a>1 set_background_opacity 1
map ctrl+shift+a>d set_background_opacity default
```

> **Note:** Background blur depends on your compositor. On Wayland with compositors like Sway or Hyprland, you configure blur on the compositor side, not in kitty.

### Config Validation and Debugging

```bash
# Show the fully resolved config (all active settings)
kitty +kitten show_config

# Check a specific setting
kitty +kitten show_config | grep clipboard_control

# Show what key presses kitty sees (useful for debugging shortcuts)
kitty +kitten show_key -m kitty

# Show key presses in raw mode (escape codes)
kitty +kitten show_key

# Show kitty's terminfo capabilities
kitty +kitten show_terminfo

# Debug info (version, OpenGL, etc.)
kitty --debug-rendering

# List all available actions for keybindings
kitty --list-actions
```

### Startup Sessions

A session file lets you define a layout of tabs and windows that opens every time you start kitty:

```conf
# In kitty.conf
startup_session ~/kitty-session.conf
```

Create `~/kitty-session.conf`:

```conf
# First tab: editor
new_tab Editor
cd ~/projects/myapp
launch nvim

# Second tab: server
new_tab Server
cd ~/projects/myapp
launch npm run dev

# Third tab: shell with splits
new_tab Shell
cd ~/projects/myapp
launch zsh

# Create a horizontal split
launch --location=hsplit zsh

# Focus the first window
focus_window 0

# Set which tab is focused on startup
focus_tab Editor
```

Launch kitty with a specific session:

```bash
kitty --session ~/kitty-session.conf
```

Or always load it via `startup_session` in `kitty.conf`.

---

## File Structure

```
~/.config/kitty/
├── kitty.conf            # Main configuration file
├── current-theme.conf    # Active color theme (Base2Tone Lavender Dark)
├── kitty.conf.bak        # Backup of original default config
└── README.md             # This documentation
```

| File | Purpose |
|------|---------|
| `kitty.conf` | Main config. All settings live here. Uses vim foldmarkers (`{{{`/`}}}`) for section folding. |
| `current-theme.conf` | Color theme, included by `kitty.conf`. Managed by `kitty +kitten themes` command. Do not edit manually. |
| `kitty.conf.bak` | Backup of the original default configuration for reference. |

---

## Configuration Breakdown

### Theme

```conf
include current-theme.conf
```

- **Active theme:** Base2Tone Lavender Dark
- **Author:** Bram de Haan
- **Palette:** Duotone blue-lavender-violet-magenta
- **Background:** `#201d2a` (dark purple-black)
- **Foreground:** `#9992b0` (muted lavender)
- **Cursor:** `#b042ff` (bright purple)

The theme is stored in `current-theme.conf` and included into the main config. This separation keeps theme colors manageable and allows easy swapping via `kitty +kitten themes`.

### Fonts

```conf
font_size 12.0
```

| Setting | Value | Description |
|---------|-------|-------------|
| `font_size` | `12.0` | Font size in points. |
| `font_family` | *(commented out)* | Defaults to system monospace. Uncomment and set to your preferred font. |

**Recommended coding fonts:**

| Font | Install (Fedora) |
|------|-----------------|
| JetBrains Mono | `sudo dnf install jetbrains-mono-fonts` |
| Fira Code | `sudo dnf install fira-code-fonts` |
| Cascadia Code | Download from GitHub or use Nerd Font variant |
| Hack Nerd Font | Download from [nerdfonts.com](https://www.nerdfonts.com/) |

To set a font, uncomment and edit these lines in `kitty.conf`:

```conf
font_family      JetBrains Mono
bold_font        auto
italic_font      auto
bold_italic_font auto
```

To browse and preview fonts interactively:

```bash
kitty +kitten choose-fonts
```

### Clipboard and Selection

```conf
clipboard_control write-clipboard write-primary read-clipboard read-primary
copy_on_select clipboard
```

| Setting | Value | Description |
|---------|-------|-------------|
| `clipboard_control` | `write-clipboard write-primary read-clipboard read-primary` | Grants kitty full read/write access to both the system clipboard and the primary selection buffer. **This is the key setting that fixes copy issues.** |
| `copy_on_select` | `clipboard` | Automatically copies any text you select with the mouse to the system clipboard. No need to press a shortcut. |

**How clipboard works on Linux (X11/Wayland):**

- **Clipboard** (`ctrl+c`/`ctrl+v` buffer): Persistent. Survives after deselecting. This is what `copy_to_clipboard` and `paste_from_clipboard` use.
- **Primary selection** (middle-click buffer): Holds whatever is currently selected. Cleared when you deselect.
- `clipboard_control` enables access to **both**, so programs running inside kitty (like nvim) can also interact with the system clipboard.

### Keyboard Shortcuts

```conf
map ctrl+shift+c copy_to_clipboard
map ctrl+shift+v paste_from_clipboard
map ctrl+shift+equal change_font_size all +1.0
map ctrl+shift+minus change_font_size all -1.0
map ctrl+shift+backspace change_font_size all 0
```

| Shortcut | Action |
|----------|--------|
| `Ctrl+Shift+C` | Copy selected text to clipboard |
| `Ctrl+Shift+V` | Paste from clipboard |
| `Ctrl+Shift+=` | Increase font size by 1pt |
| `Ctrl+Shift+-` | Decrease font size by 1pt |
| `Ctrl+Shift+Backspace` | Reset font size to default (12pt) |

> **Note:** These are in addition to kitty's many built-in shortcuts. See all defaults with `kitty +kitten show_key -m kitty`.

**Other useful built-in shortcuts (not redefined, available by default):**

| Shortcut | Action |
|----------|--------|
| `Ctrl+Shift+Enter` | New window (split) |
| `Ctrl+Shift+T` | New tab |
| `Ctrl+Shift+Q` | Close tab |
| `Ctrl+Shift+W` | Close window |
| `Ctrl+Shift+F5` | Reload configuration |
| `Ctrl+Shift+F2` | Edit config in `$EDITOR` |
| `Ctrl+Shift+]` | Next window |
| `Ctrl+Shift+[` | Previous window |
| `Ctrl+Shift+Right` | Next tab |
| `Ctrl+Shift+Left` | Previous tab |
| `Ctrl+Shift+F` | Move window forward |
| `Ctrl+Shift+B` | Move window backward |
| `Ctrl+Shift+Up` | Scroll up |
| `Ctrl+Shift+Down` | Scroll down |
| `Ctrl+Shift+Page_Up` | Scroll page up |
| `Ctrl+Shift+Page_Down` | Scroll page down |
| `Ctrl+Shift+Home` | Scroll to top |
| `Ctrl+Shift+End` | Scroll to bottom |
| `Ctrl+Shift+H` | Browse scrollback in pager |

### Neovim Integration

```conf
allow_remote_control yes
listen_on unix:/tmp/kitty
term xterm-kitty
shell_integration enabled
confirm_os_window_close 0
```

| Setting | Value | Description |
|---------|-------|-------------|
| `allow_remote_control` | `yes` | Enables the `kitty @` remote control API. Required by nvim plugins that interact with kitty (e.g., window navigation, sending commands). |
| `listen_on` | `unix:/tmp/kitty` | Creates a Unix socket for remote control communication. Nvim plugins connect through this socket. |
| `term` | `xterm-kitty` | Sets the `$TERM` variable. `xterm-kitty` provides full feature support including undercurl, true color (24-bit), styled underlines, and strikethrough. |
| `shell_integration` | `enabled` | Enables kitty's shell integration features: command output marking, click-to-open file paths, last command output access, etc. |
| `confirm_os_window_close` | `0` | Disables the "are you sure?" prompt when closing windows. Prevents annoying confirmation when closing nvim splits or terminal windows. Set to `-1` to only confirm when processes are running. |

**Why `xterm-kitty` instead of `xterm-256color`:**

| Feature | `xterm-256color` | `xterm-kitty` |
|---------|-------------------|---------------|
| True color (24-bit) | Yes | Yes |
| Undercurl | No | Yes |
| Styled underlines | No | Yes |
| Strikethrough | Partial | Yes |
| Kitty graphics protocol | No | Yes |
| Key encoding | Legacy | Full (CSI u) |

### Scrollback

```conf
scrollback_lines 10000
```

| Setting | Value | Description |
|---------|-------|-------------|
| `scrollback_lines` | `10000` | Number of lines kept in scrollback buffer per window. Increase if you need more history. Uses ~10MB per window at this setting. |

> **Tip:** Press `Ctrl+Shift+H` to browse the full scrollback in your pager (usually `less`). You can also pipe scrollback to nvim with `kitty +kitten show_scrollback`.

### Window and Layout

```conf
window_padding_width 4
enabled_layouts splits,stack
```

| Setting | Value | Description |
|---------|-------|-------------|
| `window_padding_width` | `4` | Inner padding (in pts) between the terminal content and the window border. Adds breathing room around text. |
| `enabled_layouts` | `splits,stack` | Available window layout modes. `splits` allows horizontal/vertical splits (like nvim). `stack` shows one window fullscreen (toggle with `Ctrl+Shift+L`). |

**Layout descriptions:**

| Layout | Description |
|--------|-------------|
| `splits` | Free-form splits similar to tmux or nvim. Create horizontal/vertical splits anywhere. |
| `stack` | Only the active window is visible, fullscreen. Other windows are hidden. Useful for focusing. |

Toggle between layouts with `Ctrl+Shift+L`.

### Performance

```conf
repaint_delay 10
input_delay 3
sync_to_monitor yes
```

| Setting | Value | Description |
|---------|-------|-------------|
| `repaint_delay` | `10` | Milliseconds to wait before repainting the screen after receiving new data. Lower = more responsive, higher = better batching. Default is 10. |
| `input_delay` | `3` | Milliseconds to wait before processing keyboard input. Lower = snappier typing response. Default is 3. |
| `sync_to_monitor` | `yes` | Synchronizes rendering to monitor refresh rate. Prevents tearing. Disable if you notice input lag on high-refresh monitors. |

---

## Neovim Setup

### Required Neovim Settings

Add the following to your `init.lua` for best kitty compatibility:

```lua
-- Enable 24-bit true color (required for theme colors to match kitty)
vim.opt.termguicolors = true

-- Enable undercurl support in kitty
-- Nvim uses undercurl for diagnostics (LSP errors, warnings, etc.)
vim.cmd([[let &t_Cs = "\e[4:3m"]])
vim.cmd([[let &t_Ce = "\e[4:0m"]])

-- Use system clipboard (integrates with kitty's clipboard_control)
vim.opt.clipboard = "unnamedplus"
```

| Setting | Purpose |
|---------|---------|
| `termguicolors = true` | Use GUI colors instead of 256-color palette. Your theme colors will match exactly between nvim and kitty. |
| `t_Cs` / `t_Ce` | Escape sequences for undercurl start/end. Kitty supports styled underlines natively with `xterm-kitty`, but nvim needs these set explicitly. |
| `clipboard = "unnamedplus"` | Makes nvim yank/paste use the system clipboard. Combined with kitty's `clipboard_control`, you get seamless copy/paste between nvim, kitty, and other apps. |

### Recommended Neovim Plugins

These plugins enhance the kitty + nvim workflow:

| Plugin | Purpose | Notes |
|--------|---------|-------|
| [vim-kitty-navigator](https://github.com/knubie/vim-kitty-navigator) | Seamlessly navigate between nvim splits and kitty windows with `Ctrl+h/j/k/l` | Requires `allow_remote_control yes` and `listen_on` |
| [kitty-scrollback.nvim](https://github.com/mikesmithgh/kitty-scrollback.nvim) | Browse kitty scrollback buffer inside nvim | Requires `allow_remote_control yes` |
| [smart-splits.nvim](https://github.com/mrjones2014/smart-splits.nvim) | Resize and navigate splits across nvim and kitty | Supports kitty multiplexer natively |

---

## Common Operations

### Copy and Paste

| Action | Method |
|--------|--------|
| Copy text | Select with mouse (auto-copies) **or** select + `Ctrl+Shift+C` |
| Paste text | `Ctrl+Shift+V` |
| Paste from primary selection | Middle mouse button |
| Copy from nvim | Yank (`y`) goes to system clipboard (if `clipboard = "unnamedplus"`) |
| Paste into nvim | `p` to paste, or `Ctrl+Shift+V` in insert mode |

### Font Size

| Action | Shortcut |
|--------|----------|
| Increase | `Ctrl+Shift+=` |
| Decrease | `Ctrl+Shift+-` |
| Reset | `Ctrl+Shift+Backspace` |

### Window Management

| Action | Shortcut |
|--------|----------|
| New window (split) | `Ctrl+Shift+Enter` |
| Close window | `Ctrl+Shift+W` |
| Next window | `Ctrl+Shift+]` |
| Previous window | `Ctrl+Shift+[` |
| Toggle layout (splits/stack) | `Ctrl+Shift+L` |

### Tabs

| Action | Shortcut |
|--------|----------|
| New tab | `Ctrl+Shift+T` |
| Close tab | `Ctrl+Shift+Q` |
| Next tab | `Ctrl+Shift+Right` |
| Previous tab | `Ctrl+Shift+Left` |
| Set tab title | `Ctrl+Shift+Alt+T` |

### Reloading Configuration

```bash
# From within kitty:
Ctrl+Shift+F5

# Or from any terminal:
kill -SIGUSR1 $(pgrep -f "kitty")
```

---

## Changing the Theme

Use kitty's built-in theme browser:

```bash
kitty +kitten themes
```

This opens an interactive preview. Select a theme and it will:
1. Update `current-theme.conf` with the new colors
2. Reload automatically

You can also change the theme non-interactively:

```bash
kitty +kitten themes --reload-in=all <Theme Name>
```

> **Note:** The `# BEGIN_KITTY_THEME` / `# END_KITTY_THEME` markers in `kitty.conf` are used by the themes kitten. Do not remove or edit these lines.

---

## Troubleshooting

### Cannot Copy Text

**Symptoms:** Selecting text doesn't copy. `Ctrl+Shift+C` does nothing. Can't paste into other apps.

**Solution checklist:**

1. Verify `clipboard_control` is set:
   ```bash
   kitty +kitten show_config | grep clipboard_control
   # Should output: clipboard_control write-clipboard write-primary read-clipboard read-primary
   ```

2. Verify `copy_on_select` is set:
   ```bash
   kitty +kitten show_config | grep copy_on_select
   # Should output: copy_on_select clipboard
   ```

3. If on Wayland, ensure your compositor allows clipboard access. Some Wayland compositors restrict clipboard access for security.

4. Reload the config: `Ctrl+Shift+F5`

5. If using X11, check that `xclip` or `xsel` is installed:
   ```bash
   sudo dnf install xclip xsel
   ```

### Undercurl Not Rendering in Neovim

**Symptoms:** LSP diagnostics show plain underlines instead of wavy undercurls.

**Solution:**

1. Verify your `$TERM` is set correctly:
   ```bash
   echo $TERM
   # Should output: xterm-kitty
   ```

2. Ensure the kitty terminfo is installed:
   ```bash
   infocmp xterm-kitty > /dev/null 2>&1 && echo "OK" || echo "MISSING"
   ```
   If missing, install it:
   ```bash
   kitty +kitten show_terminfo > /tmp/kitty.terminfo
   tic -x /tmp/kitty.terminfo
   ```

3. Add the undercurl escape sequences to your `init.lua`:
   ```lua
   vim.cmd([[let &t_Cs = "\e[4:3m"]])
   vim.cmd([[let &t_Ce = "\e[4:0m"]])
   ```

### Neovim Colors Look Wrong

**Symptoms:** Colors are washed out, limited to 256 colors, or don't match your theme.

**Solution:**

1. Ensure `termguicolors` is enabled in nvim:
   ```lua
   vim.opt.termguicolors = true
   ```

2. Verify true color support:
   ```bash
   kitty +kitten show_terminfo | grep -i color
   ```

3. Do **not** set `$TERM` to `xterm-256color` in your shell config. Kitty sets it correctly via the `term` config option.

### Kitty Remote Control Not Working

**Symptoms:** `kitty @` commands fail. Nvim plugins can't communicate with kitty.

**Solution:**

1. Verify remote control is enabled:
   ```bash
   kitty +kitten show_config | grep allow_remote_control
   # Should output: allow_remote_control yes
   ```

2. Verify the socket exists:
   ```bash
   ls -la /tmp/kitty
   ```

3. Test remote control:
   ```bash
   kitty @ ls
   ```

4. If you run multiple kitty instances, each needs a unique socket. Use `$KITTY_PID` for uniqueness:
   ```conf
   listen_on unix:/tmp/kitty-{kitty_pid}
   ```

### Font Not Found

**Symptoms:** Kitty falls back to a default font. Text looks different than expected.

**Solution:**

1. List available monospace fonts:
   ```bash
   fc-list :spacing=mono family | sort -u
   ```

2. Verify your font is installed:
   ```bash
   fc-list | grep -i "JetBrains"
   ```

3. Use the exact family name from `fc-list` in your config.

4. Browse and select fonts interactively:
   ```bash
   kitty +kitten choose-fonts
   ```

### SSH Breaks with xterm-kitty

**Symptoms:** Remote commands fail, `clear` doesn't work, display is garbled after SSHing.

**Cause:** The remote server doesn't have the `xterm-kitty` terminfo entry.

**Solutions (pick one):**

1. **Best:** Use kitty's SSH kitten instead of plain `ssh`:
   ```bash
   kitty +kitten ssh user@host
   ```
   This auto-copies terminfo and sets up shell integration on the remote.

2. **Manual:** Copy terminfo once to the remote:
   ```bash
   infocmp -a xterm-kitty | ssh user@host tic -x -o ~/.terminfo /dev/stdin
   ```

3. **Quick fix:** Add to your remote `~/.bashrc` or `~/.zshrc`:
   ```bash
   [ "$TERM" = "xterm-kitty" ] && export TERM=xterm-256color
   ```
   This loses kitty-specific features (undercurl, etc.) but everything else works.

4. **Alias approach** (add to your local `~/.zshrc`):
   ```bash
   alias ssh="kitty +kitten ssh"
   ```

### Keyboard Input Not Recognized by Programs

**Symptoms:** Some key combinations don't work in nvim or other TUI programs. Keys produce wrong characters.

**Cause:** Kitty uses an enhanced keyboard protocol (CSI u) that older programs may not support.

**Solutions:**

1. Check what kitty sees when you press a key:
   ```bash
   kitty +kitten show_key -m kitty
   ```

2. If a specific program has issues, you can set `TERM` just for that program:
   ```bash
   TERM=xterm-256color problematic-program
   ```

3. In `kitty.conf`, you can map keys to send specific escape sequences:
   ```conf
   map ctrl+h send_text all \x08
   ```

### Splits or Windows Not Working as Expected

**Symptoms:** `Ctrl+Shift+Enter` does nothing, layout doesn't change, windows appear in wrong positions.

**Solutions:**

1. Verify enabled layouts:
   ```bash
   kitty +kitten show_config | grep enabled_layouts
   ```

2. The `splits` layout requires launching windows with `--location`:
   ```conf
   # Explicit split directions
   map ctrl+shift+\ launch --location=vsplit --cwd=current
   map ctrl+shift+- launch --location=hsplit --cwd=current
   ```
   Without `--location`, new windows are placed by the layout's default algorithm.

3. Switch layouts with `Ctrl+Shift+L` — this cycles through your `enabled_layouts` list.

4. If you accidentally closed all splits and can't get back, open a new window: `Ctrl+Shift+Enter`.

### Performance Issues (Lag, High CPU)

**Symptoms:** Typing feels sluggish, high CPU usage, screen tearing, slow scrolling through large output.

**Solutions:**

1. **Verify GPU rendering is active:**
   ```bash
   kitty --debug-rendering 2>&1 | head -20
   ```

2. **Adjust performance settings** in `kitty.conf`:
   ```conf
   # Already set in this config:
   repaint_delay 10      # Lower = more responsive (minimum 0)
   input_delay 3         # Lower = snappier input (minimum 0)
   sync_to_monitor yes   # Set to 'no' if you see input lag on high-refresh monitors
   ```

3. **Reduce scrollback** if memory is an issue:
   ```conf
   scrollback_lines 5000   # Default in this config is 10000
   ```

4. **Disable VSync** for high-refresh rate monitors:
   ```conf
   sync_to_monitor no
   ```

5. **If a specific program causes high CPU** (e.g., a program producing massive output):
   ```bash
   # Pipe through less or redirect to file instead
   noisy-program | less
   noisy-program > output.log 2>&1
   ```
