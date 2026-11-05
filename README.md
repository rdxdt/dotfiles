# dotfiles

My personal configuration files for **zsh**, **bash** and **vim**, written for a
Debian-based Linux system.

| File      | What it configures                                                 |
|-----------|--------------------------------------------------------------------|
| `.zshrc`  | zsh: Pure prompt, completion, autosuggestions, syntax highlighting |
| `.bashrc` | bash: Debian's default bashrc with a custom prompt and aliases     |
| `.vimrc`  | vim: indentation, search, line numbers and colour scheme           |

---

## zsh (`.zshrc`)

### Features

- **Prompt:** [Pure](https://github.com/sindresorhus/pure), a minimal async
  prompt that shows the current directory, git branch and status, and how long
  slow commands took. It is loaded from `~/.zsh/pure` through `fpath`.
- **History:** keeps 1000 lines in `~/.zsh_history`, shares history between
  open shells (`sharehistory`) and drops duplicate entries (`histignorealldups`).
- **Keybindings:** emacs mode (`bindkey -e`), even if `$EDITOR` is vi.
- **Completion:**
  - menu selection that you can scroll through with the arrow keys
  - case-insensitive matching and matching on partial words (`f-b` → `foo-bar`)
  - spelling correction and approximate matching
  - completion lists coloured with `LS_COLORS`
  - `kill <TAB>` lists your processes with PID, CPU usage and command, with PIDs in red
- **Autosuggestions:** suggests the rest of a command from your history as
  you type, shown in grey. Press <kbd>→</kbd> or <kbd>End</kbd> to accept it.
- **Syntax highlighting:** colours the command line as you type, with the
  `main`, `brackets` and `pattern` highlighters enabled. The custom colours are:
  - commands in cyan, keywords and precommands (`sudo`, `if`, …) in bold cyan
  - options (`-a`, `--all`) in green
  - quoted strings in yellow, `$var` inside double quotes in magenta
  - paths in bold, unknown commands in white
  - nested brackets in a different colour per level, and unmatched brackets in bold red
- **Aliases and environment:**
  - `ls` and `grep` with colour output
  - `ip` → `ip -c` (coloured `ip` output)
  - `GCC_COLORS` set so GCC errors, warnings and notes are coloured
  - `~/.local/bin` added to the start of `PATH`

### Dependencies

| Dependency                                                                      | Used for                 | Install (Debian/Ubuntu)                                            |
|---------------------------------------------------------------------------------|--------------------------|--------------------------------------------------------------------|
| [zsh](https://www.zsh.org/) ≥ 5.2                                               | the shell itself         | `sudo apt install zsh`                                             |
| [Pure](https://github.com/sindresorhus/pure)                                    | prompt                   | `git clone https://github.com/sindresorhus/pure.git ~/.zsh/pure`   |
| [git](https://git-scm.com/) ≥ 2.15.2                                            | git info in Pure         | `sudo apt install git`                                             |
| [zsh-autosuggestions](https://github.com/zsh-users/zsh-autosuggestions)         | history suggestions      | `sudo apt install zsh-autosuggestions`                             |
| [zsh-syntax-highlighting](https://github.com/zsh-users/zsh-syntax-highlighting) | command-line colours     | `sudo apt install zsh-syntax-highlighting`                         |
| coreutils (`dircolors`), iproute2 (`ip`)                                        | colours, `ip -c` alias   | installed by default on Debian                                     |

The two plugins are sourced from the paths the Debian packages use:

```
/usr/share/zsh-autosuggestions/zsh-autosuggestions.zsh
/usr/share/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh
```

Other distributions put them elsewhere (Arch, for example, uses
`/usr/share/zsh/plugins/<plugin>/`). If you install them from another package
manager or clone them from git, change the two `source` lines in `.zshrc`.

---

## bash (`.bashrc`)

This is the default Debian `.bashrc` with a few changes.

### Features

- **Prompt:** `user:~/current/dir $`, with the username in bold and the
  directory in cyan, on xterm-like terminals.
- **History:** ignores duplicate lines and lines that start with a space, and
  appends to the history file instead of overwriting it (1000 lines in memory,
  2000 on disk).
- **Aliases:**
  - `ls`, `grep`, `fgrep`, `egrep` with colour output
  - `ll` (`ls -alF`), `la` (`ls -A`), `l` (`ls -CF`)
  - `alert`: shows a desktop notification when a long command finishes, for
    example `sleep 10; alert`
  - `ytdl`: downloads a video as MP4 with metadata using `yt-dlp`
  - `yta`: downloads only the audio and converts it to MP3 using `yt-dlp`
- Loads `~/.bash_aliases` if it exists, and bash-completion if it is installed.

### Dependencies

| Dependency                                     | Used for                  | Install (Debian/Ubuntu)              |
|------------------------------------------------|---------------------------|--------------------------------------|
| bash-completion                                | tab completion            | `sudo apt install bash-completion`   |
| [yt-dlp](https://github.com/yt-dlp/yt-dlp)     | `ytdl` and `yta` aliases  | `sudo apt install yt-dlp` or `pipx install yt-dlp` |
| [ffmpeg](https://ffmpeg.org/)                  | MP3 conversion in `yta`   | `sudo apt install ffmpeg`            |
| libnotify-bin (`notify-send`)                  | `alert` alias             | `sudo apt install libnotify-bin`     |

The Debian package of `yt-dlp` can fall behind the upstream releases, and old
versions often stop working with YouTube. Installing it with `pipx` keeps it
current, and puts it in `~/.local/bin`, which `.zshrc` adds to `PATH`.

---

## vim (`.vimrc`)

### Features

- Syntax highlighting with the built-in `evening` colour scheme on a dark background
- Indentation: 4 spaces, with tabs converted to spaces (`expandtab`), and
  filetype-specific indent rules
- Line numbers, and the command being typed shown in the bottom right (`showcmd`)
- A menu for command-line completion (`wildmenu`)
- Highlights the matching bracket (`showmatch`)
- Search: highlights matches as you type (`incsearch`) and keeps all matches
  highlighted afterwards (`hlsearch`)
- Opens new files directly in insert mode
- Mouse support turned off, so you can select and copy text with the terminal

### Dependencies

Only vim itself (`sudo apt install vim`). The config uses no plugins, and
`evening` ships with vim.

---

## Installation

Clone the repository and link each file into your home directory. Back up any
existing files first, because `ln -sf` replaces them.

```sh
git clone <this-repo-url> ~/dotfiles
cd ~/dotfiles

for f in .zshrc .bashrc .vimrc; do
    [ -e "$HOME/$f" ] && [ ! -L "$HOME/$f" ] && mv "$HOME/$f" "$HOME/$f.bak"
    ln -sf "$PWD/$f" "$HOME/$f"
done
```

Install the dependencies listed above, then make zsh your default shell:

```sh
sudo apt install zsh zsh-autosuggestions zsh-syntax-highlighting git
git clone https://github.com/sindresorhus/pure.git ~/.zsh/pure
chsh -s "$(command -v zsh)"
```

Log out and back in (or open a new terminal) to start using zsh.
