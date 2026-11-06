# scripts

Small command-line tools, mostly shell.

| Script | What it does |
|---|---|
| [`rgn`](#rgn---ripgrep--fzf--neovim-integration) | Search code with ripgrep, pick a match in fzf, open it in Neovim |
| [`bdboard`](#bdboard---a-beads-board-for-a-tmux-pane) | Browse a [beads](https://github.com/steveyegge/beads) issue tracker as a live board in a tmux pane, with plain-language explanations from Claude |

To install, clone the repo and put it on your `PATH`, or copy the script you want into a directory that is.

## rgn - Ripgrep + FZF + Neovim Integration

A powerful command-line tool that combines ripgrep's fast text search with fzf's interactive filtering and Neovim's editing capabilities, providing a seamless code navigation experience.

### Purpose

`rgn` enhances your workflow by allowing you to:
- Search through codebases with ripgrep's blazing-fast performance
- Interactively filter results using fzf's fuzzy finder
- Preview matches with syntax highlighting via bat
- Jump directly to the selected line in Neovim

This creates a fluid experience for finding and editing code across your projects.

### Features

- **Fast Search**: Leverages ripgrep for high-performance text searching
- **Interactive Selection**: Use fzf to filter and select from search results
- **Live Preview**: See context around matches with syntax-highlighted previews
- **Direct Navigation**: Opens selected files at the exact line in Neovim
- **Smart Defaults**: Case-insensitive search that becomes case-sensitive when uppercase letters are used

### Prerequisites

The following tools must be installed:
- `rg` (ripgrep) - Fast text search
- `fzf` - Fuzzy finder
- `nvim` (Neovim) - Text editor
- `bat` - Syntax highlighting for previews

#### Installation

**macOS (Homebrew):**
```bash
brew install ripgrep fzf bat neovim
```

**Ubuntu/Debian:**
```bash
sudo apt install ripgrep fzf bat neovim
```

### Usage

```bash
## Search for a specific pattern
rgn "TODO"

## Search for function definitions
rgn "function"

## Search everything (empty pattern)
rgn
```

#### Interactive Controls

While in the fzf interface:
- **Arrow keys** or **Ctrl+j/k**: Navigate through results
- **Enter**: Open selected file in Neovim at the matched line
- **Ctrl+c** or **Esc**: Exit without opening

### How It Works

1. **Search Phase**: ripgrep searches for your pattern across all files (respecting .gitignore)
2. **Filter Phase**: Results are piped to fzf for interactive filtering
3. **Preview Phase**: bat displays syntax-highlighted previews of selected matches
4. **Edit Phase**: Pressing Enter opens Neovim at the exact line of the match

### Customization

The script can be modified to:
- Change the editor (replace `nvim` with `vim`, `code`, etc.)
- Adjust preview window size and position
- Add additional ripgrep flags for specific search behaviors
- Customize fzf keybindings

### Example Workflow

1. Run `rgn "handleError"` to find all error handling code
2. Use fzf to filter results by typing additional keywords
3. Preview matches to find the exact one you need
4. Press Enter to jump directly to that line in Neovim

This tool transforms the typically multi-step process of searching and navigating code into a single, efficient command.

## bdboard - a beads board for a tmux pane

`bdboard` shows a [beads](https://github.com/steveyegge/beads) (`bd`) project as a live board you can keep open in a tmux pane, for example next to a coding agent that is working through the beads.

### What you see

The list is split into sections:

| Section | Beads in it |
|---|---|
| IN PROCESS | Claimed (`in_progress`) and not yet delivered |
| UP NEXT | Open, with nothing blocking them |
| DELIVERED | In progress and carrying a delivered label, so waiting on a merge (see below) |
| BLOCKED | Open, and waiting on another bead; shows which |
| DONE | Closed, newest first |

Below the list, a detail pane shows the selected bead as formatted markdown: description, acceptance criteria, what it depends on, what it unblocks and its notes.

**Delivered labels.** beads has no built-in "delivered" state. Some workflows leave a bead `in_progress` after its work is committed or its pull request is open, and mark it with a label. `bdboard` treats `in_progress` beads labelled `committed` or `in-review` as delivered; set `BDBOARD_DELIVERED_LABELS` to your own labels. A blocker counts as cleared once it is closed or delivered. If you don't use such labels, DELIVERED stays empty and nothing else changes.

### Keys

| Key | Action |
|---|---|
| up / down | Move through the beads |
| type | Filter the list |
| enter | Plain-language explanation of the bead (see below) |
| ctrl-e | Regenerate the explanation |
| tab | Detail mode: `j`/`k` scroll a line, `d`/`u` half a page, `g`/`G` top or bottom; `esc` or `tab` returns to the list |
| ctrl-d / ctrl-u | Scroll the detail pane from the list |
| ctrl-p | Switch project |
| ctrl-r | Refresh now (it also refreshes every 10 seconds) |
| esc | Quit |

### Explanations (needs the Claude Code CLI)

Enter asks [Claude Code](https://claude.com/claude-code) (`claude -p`) to explain the bead for someone who isn't an engineer: what it is, what it delivers, why now, a risk table, and what to settle before starting.

- It runs read-only: Claude may only run `bd show`, `bd dep list`, `bd list`, `git log`, `git branch` and read files. It cannot edit files or change beads.
- In tmux it runs in the background with a spinner in the board's header, then opens in its own tmux window when ready (`q` closes it). Outside tmux you wait for it.
- It is cached in `~/.cache/bdboard` until the bead changes. Anything that updates the bead (a note, label or status) makes the next enter ask again.
- If the project has its own `.claude/commands/explain-bead.md`, that command is used instead of the built-in prompt.
- Each explanation is a Claude call and uses your Claude plan or API credits.

Without `claude` installed, the board works and enter tells you explanations are unavailable.

### Usage

```bash
bdboard                 # the beads project containing the current directory, else a picker
bdboard -p <path>       # a specific project
bdboard --pick          # choose a project first
bdboard --split         # open the board in a new tmux pane to the right
```

To bind it to a tmux key (prefix + B), add to `~/.tmux.conf`:

```tmux
bind B split-window -h -l 45% -c "#{pane_current_path}" "bdboard"
```

### Finding projects

The project picker (ctrl-p or `--pick`) lists:

1. Projects the board has opened before, most recent first (kept in `~/.local/state/bdboard/recent`).
2. beads projects found under `BDBOARD_ROOTS` (default: whichever of `~/Projects`, `~/projects`, `~/src`, `~/code` exist, four levels deep).

You can also type the path of any beads project into the picker. So the simplest way to add a project is to run `bdboard` once inside it.

### Requirements

| Tool | Needed for |
|---|---|
| bash, `python3`, `curl` | The script itself (present on macOS and most Linux) |
| [`bd`](https://github.com/steveyegge/beads), `jq` | Reading the tracker |
| [`fzf`](https://github.com/junegunn/fzf) 0.51+ | The board |
| [`claude`](https://claude.com/claude-code) | Explanations (optional) |
| tmux | `--split` and background explanations (optional) |
| [`mdfried`](https://github.com/benjajaja/mdfried) or [`glow`](https://github.com/charmbracelet/glow) | Showing explanations (optional; mdfried draws large headings; falls back to `less`) |

```bash
brew install jq fzf mdfried tmux
```

### Settings

| Variable | Default | Meaning |
|---|---|---|
| `BDBOARD_ROOTS` | `~/Projects:~/projects:~/src:~/code` | Colon-separated directories to search for projects |
| `BDBOARD_DELIVERED_LABELS` | `committed,in-review` | Labels that mark an in-progress bead as delivered |
| `BDBOARD_REFRESH` | `10` | Seconds between refreshes; `0` turns refresh off |
| `BDBOARD_WIDTH` | `45%` | Pane width for `--split` |

## License

MIT. See [LICENSE](LICENSE).
