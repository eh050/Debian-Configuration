I use these aliases for Debian based systems to make my life a lot easier. Check which shell you are using with `echo $SHELL`

# IF USING BASH
Create the aliases file:
```bash
cat << 'EOF' >> ~/.bash_aliases
# --- Simple aliases ---
alias c='clear'
alias cls='clear; ls'
alias home='cd "${HOME}"'
alias update='sudo apt update && sudo apt upgrade -y'
alias pymodall='chmod +x ./*py'
alias reload-shell='source ~/.bashrc'
alias vact='source .venv/bin/activate'

# --- Functions ---
mkcd() {
    if [ "$#" -ne 1 ]; then
        printf 'Usage: mkcd <directory>\n' >&2
        return 2
    fi

    mkdir -p -- "$1" && cd -- "$1"
}

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

# --- Conditional aliases ---
if command -v apt &>/dev/null; then
    alias aptup='sudo apt update'
    alias aptupgrade='sudo apt update && sudo apt upgrade'
    alias aptfull='sudo apt update && sudo apt full-upgrade'
    alias apti='sudo apt install'
    alias aptr='sudo apt remove'
    alias aptpurge='sudo apt purge'
    alias aptsearch='apt search'
    alias aptclean='sudo apt autoremove'
fi

if command -v python3 &>/dev/null; then
    alias py='python3'
    alias pyserver='python3 -m http.server'
fi
EOF
```

Then link the alias file:
```bash
grep -q '.bash_aliases' ~/.bashrc || cat << 'EOF' >> ~/.bashrc

# Load custom aliases and functions
if [ -f "${HOME}/.bash_aliases" ]; then
    source "${HOME}/.bash_aliases"
fi
EOF
```

# IF USING ZSH
Create the aliases file:
```bash
cat << 'EOF' >> ~/.zsh_aliases
# --- Simple aliases ---
alias c='clear'
alias cls='clear; ls'
alias home='cd "${HOME}"'
alias update='sudo apt update && sudo apt upgrade -y'
alias pymodall='chmod +x ./*py'
alias reload-shell='source ~/.zshrc'
alias vact='source .venv/bin/activate'

# --- Functions ---
mkcd() {
    if [ "$#" -ne 1 ]; then
        printf 'Usage: mkcd <directory>\n' >&2
        return 2
    fi

    mkdir -p -- "$1" && cd -- "$1"
}

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

# --- Conditional aliases ---
if command -v apt &>/dev/null; then
    alias aptup='sudo apt update'
    alias aptupgrade='sudo apt update && sudo apt upgrade'
    alias aptfull='sudo apt update && sudo apt full-upgrade'
    alias apti='sudo apt install'
    alias aptr='sudo apt remove'
    alias aptpurge='sudo apt purge'
    alias aptsearch='apt search'
    alias aptclean='sudo apt autoremove'
fi

if command -v python3 &>/dev/null; then
    alias py='python3'
    alias pyserver='python3 -m http.server'
fi
EOF
```

Then link the file:
```bash
cat << 'EOF' >> ~/.zshrc

# Load custom aliases and functions
if [ -f "${HOME}/.zsh_aliases" ]; then
    source "${HOME}/.zsh_aliases"
fi
EOF
```
