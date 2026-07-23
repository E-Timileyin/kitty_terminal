# Daily Driver: kitty + tmux + Neovim

The complete reference for this development environment — style, keymaps, and
how the three layers integrate. Built for backend / systems / infra work on
Fedora Linux.

**The stack, and each layer's single job:**

| Layer | Config | Job |
|---|---|---|
| **kitty** | `~/.config/kitty/kitty.conf` | GPU-accelerated renderer: fonts, colors, clipboard, scrollback |
| **tmux** | `~/.config/tmux/tmux.conf` | Multiplexer: sessions, windows, panes, persistence across disconnects |
| **Neovim** | `~/.config/nvim/` (NvChad v2.5) | Editor: LSP, treesitter, git, fuzzy finding |

kitty deliberately does **not** use its own tabs/splits — tmux owns
multiplexing. This matters for infra work: a tmux session survives an SSH
drop, a terminal crash, or a reboot of your laptop while attached to a
remote box. Kitty splits don't.

---

## Table of Contents

- [Style](#style)
- [Kitty Keymaps](#kitty-keymaps)
- [Tmux Keymaps](#tmux-keymaps)
- [Neovim Keymaps](#neovim-keymaps)
- [Seamless Navigation](#seamless-navigation-the-integration-core)
- [Clipboard Flow](#clipboard-flow)
- [Reloading Configs](#reloading-configs)
- [SSH & Remote Work](#ssh--remote-work)
- [Troubleshooting](#troubleshooting)

---

## Style

**Theme: Aura Dracula Spirit (Soft)** — by
[JoseMurilloc](https://github.com/JoseMurilloc/aura-spirit-dracula), applied
identically across all three layers so panes, statuslines, and editor UI read
as one surface:

- kitty → `aura-theme.conf` (included from `kitty.conf`)
- tmux → theme variables in `tmux.conf` (status bar, borders, copy mode)
- nvim → `lua/chadrc.lua` (base46 override with exact VS Code token colors)

### Palette

| Role | Hex | Used for |
|---|---|---|
| Background | `#191521` | Editor/terminal background |
| Deep background | `#14111b` | Sidebars, tab bars, statusline |
| Foreground | `#edecee` | Default text |
| Purple | `#a277ff` | Keywords, active borders, cursor, accents |
| Green | `#61ffca` | Strings, constants, numbers |
| Orange | `#ffca85` | Functions, methods, warnings |
| Cyan | `#82e2ff` | Types, classes, URLs |
| Pink | `#f694ff` | Properties, attributes, control flow |
| Red | `#ff6767` | Errors, deletes |
| Comment | `#64548E` | Comments (muted purple, italic) |
| Selection | `#3d375e` | Visual selection |
| Gray | `#2e2b38` | Inactive borders, subtle UI |

The previous kitty theme (Base2Tone Lavender Dark) is kept in
`current-theme.conf` if you ever want it back — swap the `include` line in
`kitty.conf`.

### Config style conventions

All three configs follow the same documentation style — keep it when editing:

- A **quick-reference comment header** at the top of tmux.conf / mappings.lua
- **Section banners** (`{{{ }}}` fold markers in kitty, `====` banners in tmux,
  `── x ──` rules in lua)
- Every non-obvious line gets a *why* comment, not just a *what*

---

## Kitty Keymaps

Kitty keeps a **minimal custom set** — its defaults remain active underneath
(nothing is cleared with `clear_all_shortcuts`).

### Configured in kitty.conf

| Key | Action |
|---|---|
| `Ctrl+Shift+C` | Copy to clipboard |
| `Ctrl+Shift+V` | Paste from clipboard |
| `Ctrl+Shift+=` | Font size +1 |
| `Ctrl+Shift+-` | Font size −1 |
| `Ctrl+Shift+Backspace` | Reset font size |
| `Shift+Up / Down` | Scroll one line |
| `Shift+PgUp / PgDn` | Scroll one page |
| `Shift+Home / End` | Scroll to top / bottom |

> Note: scrollback keys only matter *outside* tmux. Inside tmux, use
> `Ctrl-a [` (copy mode) — tmux owns the scrollback there.

### Useful kitty defaults (still active)

| Key | Action |
|---|---|
| `Ctrl+Shift+F5` | Reload kitty.conf in place |
| `Ctrl+Shift+F2` | Open kitty.conf in editor |
| `Ctrl+Shift+E` | Open URL hints (click URLs by keyboard) |
| `Ctrl+Shift+U` | Unicode/emoji input |
| `Ctrl+Shift+F11` | Toggle fullscreen |
| `Ctrl+Shift+Esc` | Kitty shell (debug/remote control) |

### Behavior settings that matter

| Setting | Value | Why |
|---|---|---|
| `copy_on_select` | `clipboard` | Mouse-select instantly copies |
| `clipboard_control` | read+write, clipboard+primary | Fixes clipboard sync both directions |
| `allow_remote_control` + `listen_on` | `unix:/tmp/kitty` | Lets nvim/scripts drive kitty (`kitten @`) |
| `shell_integration` | `enabled` | Prompt jumping, cwd-aware windows |
| `scrollback_lines` | 10000 | Deep history outside tmux |
| `repaint_delay 10` / `input_delay 3` | | Low-latency typing feel |

### Optional: auto-attach tmux

`kitty.conf` has a commented line — uncomment to make every kitty window drop
straight into a persistent tmux session:

```conf
shell tmux new-session -A -s main
```

---

## Tmux Keymaps

**Prefix: `Ctrl-a`** (not the default Ctrl-b).

### Sessions

| Key | Action |
|---|---|
| `Ctrl-a Ctrl-c` | New session |
| `Ctrl-a S` | Switch session (tree view) |
| `Ctrl-a $` | Rename session |
| `Ctrl-a X` | Kill session (confirms) |
| `Ctrl-a d` | Detach (session keeps running) |

### Windows

| Key | Action |
|---|---|
| `Ctrl-a c` | New window (inherits cwd) |
| `Alt+1..9` | Jump to window N (no prefix) |
| `Ctrl-a p / n` | Previous / next window |
| `Ctrl-a ,` | Rename window |
| `Ctrl-a < / >` | Swap window left / right |
| `Ctrl-a &` | Kill window |

### Panes

| Key | Action |
|---|---|
| `Ctrl-a \|` | Split side-by-side (inherits cwd) |
| `Ctrl-a -` | Split top/bottom (inherits cwd) |
| **`Ctrl+h/j/k/l`** | **Navigate panes and nvim splits, seamless, no prefix** |
| **`Alt+h/j/k/l`** | Same, alternate modifier |
| `Ctrl-a h/j/k/l` | Navigate panes (prefixed fallback) |
| `Ctrl-a H/J/K/L` | Resize pane by 5 (repeatable) |
| `Ctrl-a m` | Zoom pane (maximize/restore) |
| `Ctrl-a x` | Kill pane (confirms) |
| `Ctrl-a Ctrl-l` | Send a real Ctrl-l (clear screen) |

### Copy mode (vi-style)

| Key | Action |
|---|---|
| `Ctrl-a [` | Enter copy mode |
| `v` | Begin selection |
| `Ctrl-v` | Rectangle selection |
| `y` | Yank to system clipboard & exit |
| `Ctrl+h/j/k/l` | Leave copy mode into another pane |

### Misc

| Key | Action |
|---|---|
| `Ctrl-a r` | Reload tmux.conf |
| Mouse | Scroll, click panes/windows, drag borders |

---

## Neovim Keymaps

**Leader: `Space` · Escape: `jk`** — full list in
`~/.config/nvim/lua/mappings.lua`; NvChad defaults also apply.

### Design principles

- Vim-native motions everywhere (`h/j/k/l`)
- Mnemonic leader groups: `g` = git, `f` = find, `h` = harpoon
- `[x` / `]x` bracket pairs for prev/next (diagnostics `d`, todos `t`, hunks `h`)
- `Ctrl+hjkl` = the same navigation keys as tmux (seamless)

### Highlights

| Key | Action |
|---|---|
| `Ctrl+h/j/k/l`, `Alt+h/j/k/l` | Navigate splits ↔ tmux panes |
| `Ctrl+s` | Save |
| `gd / gD / gr / gi` | LSP definition / declaration / references / implementation |
| `K` | Hover docs |
| `<leader>rn / ca` | Rename / code action |
| `[d` / `]d` | Prev / next diagnostic |
| `<leader>fm` | Format (conform, LSP fallback) |
| `<leader>fs / fS / fd / ft` | Telescope: symbols / workspace symbols / diagnostics / TODOs |
| `<leader>ha / hh` | Harpoon add / menu |
| `<leader>1..4` | Harpoon file 1–4 |
| `<leader>gp / gb / gs / gr` | Git preview / blame / stage / reset hunk |
| `[h` / `]h` | Prev / next git hunk |
| `<leader>u` | Undotree |
| `Ctrl+y` (insert) | Accept Copilot suggestion |

---

## Seamless Navigation (the integration core)

One set of keys — `Ctrl+h/j/k/l` (or `Alt+h/j/k/l`) — moves focus across
**both** nvim splits and tmux panes as if they were one grid.

How it works:

1. **tmux side** (`tmux.conf`): each key runs an `is_vim` check — it inspects
   the foreground process on the current pane's TTY. If it's nvim (or fzf),
   tmux *forwards the keypress* into the pane; otherwise tmux moves panes
   itself.
2. **nvim side** (`vim-tmux-navigator` plugin, mappings in `mappings.lua`):
   `Ctrl+h` tries to move to the nvim split on the left. If there isn't one
   (you're at nvim's edge), the plugin shells out to `tmux select-pane -L`.
3. **Outside tmux** the plugin detects no `$TMUX` and falls back to plain
   `<C-w>` navigation — nothing breaks.

The one tradeoff: `Ctrl+l` no longer clears the shell (it navigates).
Use `Ctrl-a Ctrl-l` or type `clear`.

## Clipboard Flow

Copying works from every layer into the system clipboard:

- **nvim yank** → OSC 52 escape sequence → tmux (`set-clipboard on`) → kitty
  (`clipboard_control write-clipboard`) → system clipboard
- **tmux copy-mode `y`** → same path
- **Mouse select in kitty** → `copy_on_select clipboard` → instant copy
- `allow-passthrough on` in tmux additionally lets kitty-specific protocols
  (e.g. the graphics protocol for image previews in nvim) tunnel through tmux

---

## Reloading Configs

| Layer | How |
|---|---|
| kitty | `Ctrl+Shift+F5` (or restart kitty) |
| tmux | `Ctrl-a r` (message: "Config reloaded!") |
| nvim | Restart nvim; plugins: `:Lazy sync` |

---

## SSH & Remote Work

Daily infra reality: remote boxes don't know what `xterm-kitty` is, and
you'll see `'xterm-kitty': unknown terminal type` on servers.

**Preferred:** use kitty's SSH kitten — it copies the terminfo automatically:

```sh
kitten ssh user@host
# alias it:  alias ssh="kitten ssh"
```

**Fallback** for weird boxes / serial consoles:

```sh
TERM=xterm-256color ssh user@host
```

**Recommended remote pattern:** run tmux on the *server*, attach from kitty.
The session — builds, tails, migrations — survives your laptop sleeping:

```sh
kitten ssh user@host -t tmux new -A -s work
```

(Nested tmux: your local prefix is `Ctrl-a`; if you also run tmux locally,
press `Ctrl-a Ctrl-a` to send the prefix to the inner/remote tmux.)

---

## Troubleshooting

| Symptom | Fix |
|---|---|
| Colors look washed out in nvim inside tmux | Confirm `echo $TERM` says `tmux-256color` inside tmux and `:checkhealth` in nvim shows truecolor OK |
| `Ctrl+l` doesn't clear the shell | By design — use `Ctrl-a Ctrl-l` |
| Ctrl+hjkl stops working after nvim crash | tmux's `is_vim` sees a dead nvim; `Ctrl-a h/j/k/l` still works, or kill the pane |
| Clipboard empty after yank over SSH | Ensure remote tmux ≥3.2 with `set -g set-clipboard on`; OSC 52 must pass through |
| `unknown terminal type` on a server | `kitten ssh` (see above) |
| Theme changed after running `kitten themes` | The kitten rewrites the `BEGIN_KITTY_THEME` block; re-point the include to `aura-theme.conf` |

---

*Configs are git-tracked. After any change that survives a day of use:
commit it. Future-you is the primary consumer of this repo.*
