# rgn - Ripgrep + FZF + Neovim Integration

A powerful command-line tool that combines ripgrep's fast text search with fzf's interactive filtering and Neovim's editing capabilities, providing a seamless code navigation experience.

## Purpose

`rgn` enhances your workflow by allowing you to:
- Search through codebases with ripgrep's blazing-fast performance
- Interactively filter results using fzf's fuzzy finder
- Preview matches with syntax highlighting via bat
- Jump directly to the selected line in Neovim

This creates a fluid experience for finding and editing code across your projects.

## Features

- **Fast Search**: Leverages ripgrep for high-performance text searching
- **Interactive Selection**: Use fzf to filter and select from search results
- **Live Preview**: See context around matches with syntax-highlighted previews
- **Direct Navigation**: Opens selected files at the exact line in Neovim
- **Smart Defaults**: Case-insensitive search that becomes case-sensitive when uppercase letters are used

## Prerequisites

The following tools must be installed:
- `rg` (ripgrep) - Fast text search
- `fzf` - Fuzzy finder
- `nvim` (Neovim) - Text editor
- `bat` - Syntax highlighting for previews

### Installation

**macOS (Homebrew):**
```bash
brew install ripgrep fzf bat neovim
```

**Ubuntu/Debian:**
```bash
sudo apt install ripgrep fzf bat neovim
```

## Usage

```bash
# Search for a specific pattern
rgn "TODO"

# Search for function definitions
rgn "function"

# Search everything (empty pattern)
rgn
```

### Interactive Controls

While in the fzf interface:
- **Arrow keys** or **Ctrl+j/k**: Navigate through results
- **Enter**: Open selected file in Neovim at the matched line
- **Ctrl+c** or **Esc**: Exit without opening

## How It Works

1. **Search Phase**: ripgrep searches for your pattern across all files (respecting .gitignore)
2. **Filter Phase**: Results are piped to fzf for interactive filtering
3. **Preview Phase**: bat displays syntax-highlighted previews of selected matches
4. **Edit Phase**: Pressing Enter opens Neovim at the exact line of the match

## Customization

The script can be modified to:
- Change the editor (replace `nvim` with `vim`, `code`, etc.)
- Adjust preview window size and position
- Add additional ripgrep flags for specific search behaviors
- Customize fzf keybindings

## Example Workflow

1. Run `rgn "handleError"` to find all error handling code
2. Use fzf to filter results by typing additional keywords
3. Preview matches to find the exact one you need
4. Press Enter to jump directly to that line in Neovim

This tool transforms the typically multi-step process of searching and navigating code into a single, efficient command.