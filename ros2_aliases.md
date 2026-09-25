ALIAS_FILE="${HOME}/.bash_aliases"
WS="${HOME}/dev_ws"   # <-- change if your workspace path is different
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

echo "Installing ROS2 aliases into $ALIAS_FILE ..."

# --- Workspace navigation ---
add_alias cw "cd ${WS}"
add_alias cs "cd ${WS}/src"
add_alias cb "cd ${WS}/build"
add_alias ci "cd ${WS}/install"

# --- Build / source ---
add_alias rebuild "cd ${WS} && colcon build --symlink-install && source ${WS}/install/setup.bash"
add_alias colbuild "colcon build --symlink-install"
add_alias colbuild1 'colcon build --symlink-install --packages-select'
add_alias coltest 'colcon test && colcon test-result --verbose'
add_alias sws "source ${WS}/install/setup.bash"

# --- Introspection ---
add_alias ros2tl 'ros2 topic list'
add_alias ros2te 'ros2 topic echo'
add_alias ros2ti 'ros2 topic info'
add_alias ros2nl 'ros2 node list'
add_alias ros2ni 'ros2 node info'
add_alias ros2sl 'ros2 service list'
add_alias ros2il 'ros2 interface list'
add_alias ros2pl 'ros2 param list'
add_alias rqtgraph 'rqt_graph'

# --- Recording / playback ---
add_alias bagrec 'ros2 bag record -a'
add_alias bagplay 'ros2 bag play'
add_alias baginfo 'ros2 bag info'

# --- Process cleanup ---
add_alias killgazebo 'pkill -9 -f gazebo; pkill -9 -f gzserver; pkill -9 -f gzclient'
add_alias killros 'pkill -9 -f ros2; pkill -9 -f rviz2'

# --- Functions ---
if add_function rosclean; then
cat << 'EOF' >> "$ALIAS_FILE"
rosclean() {
    local ws="${1:-$WS}"
    if [ -z "$ws" ] || [ ! -d "$ws" ]; then
        printf 'Usage: rosclean <workspace-path>\n' >&2
        return 2
    fi
    rm -rf -- "$ws/build" "$ws/install" "$ws/log"
    echo "Cleaned build/install/log in $ws"
}
EOF
    echo "  added:         function rosclean"
fi

if add_function cbuild; then
cat << 'EOF' >> "$ALIAS_FILE"
cbuild() {
    if [ "$#" -eq 0 ]; then
        printf 'Usage: cbuild <package1> [package2 ...]\n' >&2
        return 2
    fi
    colcon build --symlink-install --packages-select "$@"
}
EOF
    echo "  added:         function cbuild"
fi

echo "Done."
