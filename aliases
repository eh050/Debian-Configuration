# Shell Aliases & Functions (Debian-based systems)

Check which shell you're using first: `echo $SHELL`

These installers are **non-destructive**: each alias/function is checked
against your existing `~/.bash_aliases` or `~/.zsh_aliases` before being
added. If it's already defined, it's skipped instead of duplicated or
overwritten.

---

## IF USING BASH

Run this whole block — it builds `~/.bash_aliases` safely:

```bash
ALIAS_FILE="${HOME}/.bash_aliases"
touch "$ALIAS_FILE"

# --- helper: only appends an alias if the name isn't already defined ---
add_alias() {
    local name="$1" cmd="$2"
    if grep -qE "^[[:space:]]*alias[[:space:]]+${name}=" "$ALIAS_FILE"; then
        echo "  skip (exists): alias $name"
    else
        printf "alias %s='%s'\n" "$name" "$cmd" >> "$ALIAS_FILE"
        echo "  added:         alias $name"
    fi
}

# --- helper: only appends a function block if the name isn't already defined ---
add_function() {
    local name="$1"
    if grep -qE "^[[:space:]]*(function[[:space:]]+)?${name}[[:space:]]*\(\)" "$ALIAS_FILE"; then
        echo "  skip (exists): function $name"
        return 1
    fi
    return 0
}

echo "Installing aliases into $ALIAS_FILE ..."

# --- Simple QOL aliases ---
add_alias c 'clear'
add_alias cls 'clear; ls'
add_alias home 'cd "${HOME}"'
add_alias update 'sudo apt update && sudo apt upgrade -y'
add_alias pymodall 'chmod +x ./*py'
add_alias reload-shell 'source ~/.bashrc'
add_alias vact 'source .venv/bin/activate'

# --- Navigation ---
add_alias .. 'cd ..'
add_alias ... 'cd ../..'
add_alias .... 'cd ../../..'
add_alias - 'cd -'

# --- ls / listing ---
add_alias ll 'ls -alFh'
add_alias la 'ls -A'
add_alias l 'ls -CF'
add_alias lt 'ls -alFht'

# --- Safety nets ---
add_alias cp 'cp -i'
add_alias mv 'mv -i'
add_alias rm 'rm -i'
add_alias mkdir 'mkdir -pv'

# --- grep / search with color ---
add_alias grep 'grep --color=auto'
add_alias egrep 'egrep --color=auto'
add_alias fgrep 'fgrep --color=auto'

# --- System info ---
add_alias df 'df -h'
add_alias du 'du -h'
add_alias free 'free -h'
add_alias path 'echo -e ${PATH//:/\\n}'
add_alias myip 'curl -s ifconfig.me'
add_alias localip "hostname -I | awk '{print \$1}'"
add_alias ports 'sudo ss -tulpn'
add_alias meminfo 'free -m -l -t'
add_alias cpuinfo 'lscpu'

# --- History / process ---
add_alias h 'history'
add_alias j 'jobs -l'
add_alias please 'sudo $(fc -ln -1)'
add_alias psg 'ps aux | grep -v grep | grep -i'

# --- Git shortcuts ---
add_alias gs 'git status'
add_alias ga 'git add'
add_alias gc 'git commit'
add_alias gp 'git push'
add_alias gl "git log --oneline --graph --decorate --all"
add_alias gd 'git diff'
add_alias gco 'git checkout'

# --- Python / venv ---
if command -v python3 &>/dev/null; then
    add_alias py 'python3'
    add_alias pyserver 'python3 -m http.server'
    add_alias venv 'python3 -m venv .venv'
fi

# --- apt (conditional) ---
if command -v apt &>/dev/null; then
    add_alias aptup 'sudo apt update'
    add_alias aptupgrade 'sudo apt update && sudo apt upgrade'
    add_alias aptfull 'sudo apt update && sudo apt full-upgrade'
    add_alias apti 'sudo apt install'
    add_alias aptr 'sudo apt remove'
    add_alias aptpurge 'sudo apt purge'
    add_alias aptsearch 'apt search'
    add_alias aptclean 'sudo apt autoremove'
fi

# --- Functions ---
if add_function mkcd; then
cat << 'EOF' >> "$ALIAS_FILE"
mkcd() {
    if [ "$#" -ne 1 ]; then
        printf 'Usage: mkcd <directory>\n' >&2
        return 2
    fi
    mkdir -p -- "$1" && cd -- "$1"
}
EOF
    echo "  added:         function mkcd"
fi

if add_function extract; then
cat << 'EOF' >> "$ALIAS_FILE"
extract() {
    if [ "$#" -ne 1 ]; then
        printf 'Usage: extract <archive>\n' >&2
        return 2
    fi

    local archive="$1"

    if [ ! -f "$archive" ]; then
        printf 'File not found: %s\n' "$archive" >&2
        return 1
    fi

    case "$archive" in
        *.tar.bz2|*.tbz2) tar -xjf "$archive" ;;
        *.tar.gz|*.tgz)   tar -xzf "$archive" ;;
        *.tar.xz|*.txz)   tar -xJf "$archive" ;;
        *.tar.zst)        tar --zstd -xf "$archive" ;;
        *.tar)            tar -xf "$archive" ;;
        *.bz2)            bunzip2 "$archive" ;;
        *.gz)             gunzip "$archive" ;;
        *.xz)             unxz "$archive" ;;
        *.zip)            unzip "$archive" ;;
        *.7z)             7z x "$archive" ;;
        *.rar)            unrar x "$archive" ;;
        *)
            printf 'Unsupported archive format: %s\n' "$archive" >&2
            return 1
            ;;
    esac
}
EOF
    echo "  added:         function extract"
fi

if add_function ff; then
cat << 'EOF' >> "$ALIAS_FILE"
ff() {
    if [ "$#" -ne 1 ]; then
        printf 'Usage: ff <name-pattern>\n' >&2
        return 2
    fi
    find . -iname "*$1*" 2>/dev/null
}
EOF
    echo "  added:         function ff"
fi

if add_function bigfiles; then
cat << 'EOF' >> "$ALIAS_FILE"
bigfiles() {
    local n="${1:-15}"
    find . -type f -exec du -h {} + 2>/dev/null | sort -rh | head -n "$n"
}
EOF
    echo "  added:         function bigfiles"
fi

if add_function backup; then
cat << 'EOF' >> "$ALIAS_FILE"
backup() {
    if [ "$#" -ne 1 ]; then
        printf 'Usage: backup <file>\n' >&2
        return 2
    fi
    cp -a -- "$1" "$1.bak.$(date +%Y%m%d%H%M%S)"
}
EOF
    echo "  added:         function backup"
fi

echo "Done."
```

Then link the alias file (only adds the source line if it's not already there):

```bash
grep -q '.bash_aliases' ~/.bashrc || cat << 'EOF' >> ~/.bashrc

# Load custom aliases and functions
if [ -f "${HOME}/.bash_aliases" ]; then
    source "${HOME}/.bash_aliases"
fi
EOF

source ~/.bashrc
```

---

## IF USING ZSH

Same idea, targeting `~/.zsh_aliases`:

```bash
ALIAS_FILE="${HOME}/.zsh_aliases"
touch "$ALIAS_FILE"

add_alias() {
    local name="$1" cmd="$2"
    if grep -qE "^[[:space:]]*alias[[:space:]]+${name}=" "$ALIAS_FILE"; then
        echo "  skip (exists): alias $name"
    else
        printf "alias %s='%s'\n" "$name" "$cmd" >> "$ALIAS_FILE"
        echo "  added:         alias $name"
    fi
}

add_function() {
    local name="$1"
    if grep -qE "^[[:space:]]*(function[[:space:]]+)?${name}[[:space:]]*\(\)" "$ALIAS_FILE"; then
        echo "  skip (exists): function $name"
        return 1
    fi
    return 0
}

echo "Installing aliases into $ALIAS_FILE ..."

# --- Simple QOL aliases ---
add_alias c 'clear'
add_alias cls 'clear; ls'
add_alias home 'cd "${HOME}"'
add_alias update 'sudo apt update && sudo apt upgrade -y'
add_alias pymodall 'chmod +x ./*py'
add_alias reload-shell 'source ~/.zshrc'
add_alias vact 'source .venv/bin/activate'

# --- Navigation ---
add_alias .. 'cd ..'
add_alias ... 'cd ../..'
add_alias .... 'cd ../../..'
add_alias - 'cd -'

# --- ls / listing ---
add_alias ll 'ls -alFh'
add_alias la 'ls -A'
add_alias l 'ls -CF'
add_alias lt 'ls -alFht'

# --- Safety nets ---
add_alias cp 'cp -i'
add_alias mv 'mv -i'
add_alias rm 'rm -i'
add_alias mkdir 'mkdir -pv'

# --- grep / search with color ---
add_alias grep 'grep --color=auto'
add_alias egrep 'egrep --color=auto'
add_alias fgrep 'fgrep --color=auto'

# --- System info ---
add_alias df 'df -h'
add_alias du 'du -h'
add_alias free 'free -h'
add_alias path 'echo -e ${PATH//:/\\n}'
add_alias myip 'curl -s ifconfig.me'
add_alias localip "hostname -I | awk '{print \$1}'"
add_alias ports 'sudo ss -tulpn'
add_alias meminfo 'free -m -l -t'
add_alias cpuinfo 'lscpu'

# --- History / process ---
add_alias h 'history'
add_alias j 'jobs -l'
add_alias please 'sudo $(fc -ln -1)'
add_alias psg 'ps aux | grep -v grep | grep -i'

# --- Git shortcuts ---
add_alias gs 'git status'
add_alias ga 'git add'
add_alias gc 'git commit'
add_alias gp 'git push'
add_alias gl "git log --oneline --graph --decorate --all"
add_alias gd 'git diff'
add_alias gco 'git checkout'

# --- Python / venv ---
if command -v python3 &>/dev/null; then
    add_alias py 'python3'
    add_alias pyserver 'python3 -m http.server'
    add_alias venv 'python3 -m venv .venv'
fi

# --- apt (conditional) ---
if command -v apt &>/dev/null; then
    add_alias aptup 'sudo apt update'
    add_alias aptupgrade 'sudo apt update && sudo apt upgrade'
    add_alias aptfull 'sudo apt update && sudo apt full-upgrade'
    add_alias apti 'sudo apt install'
    add_alias aptr 'sudo apt remove'
    add_alias aptpurge 'sudo apt purge'
    add_alias aptsearch 'apt search'
    add_alias aptclean 'sudo apt autoremove'
fi

# --- Functions ---
if add_function mkcd; then
cat << 'EOF' >> "$ALIAS_FILE"
mkcd() {
    if [ "$#" -ne 1 ]; then
        printf 'Usage: mkcd <directory>\n' >&2
        return 2
    fi
    mkdir -p -- "$1" && cd -- "$1"
}
EOF
    echo "  added:         function mkcd"
fi

if add_function extract; then
cat << 'EOF' >> "$ALIAS_FILE"
extract() {
    if [ "$#" -ne 1 ]; then
        printf 'Usage: extract <archive>\n' >&2
        return 2
    fi

    local archive="$1"

    if [ ! -f "$archive" ]; then
        printf 'File not found: %s\n' "$archive" >&2
        return 1
    fi

    case "$archive" in
        *.tar.bz2|*.tbz2) tar -xjf "$archive" ;;
        *.tar.gz|*.tgz)   tar -xzf "$archive" ;;
        *.tar.xz|*.txz)   tar -xJf "$archive" ;;
        *.tar.zst)        tar --zstd -xf "$archive" ;;
        *.tar)            tar -xf "$archive" ;;
        *.bz2)            bunzip2 "$archive" ;;
        *.gz)             gunzip "$archive" ;;
        *.xz)             unxz "$archive" ;;
        *.zip)            unzip "$archive" ;;
        *.7z)             7z x "$archive" ;;
        *.rar)            unrar x "$archive" ;;
        *)
            printf 'Unsupported archive format: %s\n' "$archive" >&2
            return 1
            ;;
    esac
}
EOF
    echo "  added:         function extract"
fi

if add_function ff; then
cat << 'EOF' >> "$ALIAS_FILE"
ff() {
    if [ "$#" -ne 1 ]; then
        printf 'Usage: ff <name-pattern>\n' >&2
        return 2
    fi
    find . -iname "*$1*" 2>/dev/null
}
EOF
    echo "  added:         function ff"
fi

if add_function bigfiles; then
cat << 'EOF' >> "$ALIAS_FILE"
bigfiles() {
    local n="${1:-15}"
    find . -type f -exec du -h {} + 2>/dev/null | sort -rh | head -n "$n"
}
EOF
    echo "  added:         function bigfiles"
fi

if add_function backup; then
cat << 'EOF' >> "$ALIAS_FILE"
backup() {
    if [ "$#" -ne 1 ]; then
        printf 'Usage: backup <file>\n' >&2
        return 2
    fi
    cp -a -- "$1" "$1.bak.$(date +%Y%m%d%H%M%S)"
}
EOF
    echo "  added:         function backup"
fi

echo "Done."
```

Then link the file (only adds the source line if it's not already there):

```bash
grep -q '.zsh_aliases' ~/.zshrc || cat << 'EOF' >> ~/.zshrc

# Load custom aliases and functions
if [ -f "${HOME}/.zsh_aliases" ]; then
    source "${HOME}/.zsh_aliases"
fi
EOF

source ~/.zshrc
```

---

## Notes

- Re-running either block is safe — it will only add whatever's still
  missing and print `skip (exists)` for anything already there.
- `add_alias`/`add_function` only check the target dotfile itself, not your
  live shell session — so if you `alias foo='bar'` directly in a terminal
  without saving it to the file, running this again will still add it.
- A few of the new aliases (`cp`, `mv`, `rm`, `mkdir`) shadow the base
  commands with safer defaults (confirm-before-overwrite, verbose mkdir).
  Drop those `add_alias` lines if you don't want that behavior.
