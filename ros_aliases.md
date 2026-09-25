I use these aliases for Debian based systems to make my life a lot easier. Check which shell you are using with `echo $SHELL`

# IF USING BASH
Create the aliases file:
```bash
cat << 'EOF' > ~/.bash_aliases
alias rebuild='cd ~/dev_ws && colcon build --symlink-install && source ~/dev_ws/install/setup.bash'
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
