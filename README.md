# difiyunk
CODE:
Paste the following code into the script:

Bash

#!/bin/bash

# Define paths
ACTIVE_LINK="$HOME/.config/qtile"
SLEEK_PATH="$HOME/dotfiles/config-sleek/qtile"
TWE_PATH="$HOME/dotfiles/config-twe/qtile"

# Check the current active configuration
CURRENT_TARGET=$(readlink "$ACTIVE_LINK")

if [ "$CURRENT_TARGET" == "$SLEEK_PATH" ]; then
    # Switch to TWE Mode
    rm "$ACTIVE_LINK"
    ln -s "$TWE_PATH" "$ACTIVE_LINK"
    echo "Switched to TWE Mode. Restarting Qtile..."
else
    # Switch to Sleek Mode
    rm "$ACTIVE_LINK"
    ln -s "$SLEEK_PATH" "$ACTIVE_LINK"
    echo "Switched to Sleek Mode. Restarting Qtile..."
fi

# Restart Qtile to apply the new config immediately
qtile cmd-obj -o cmd -f restart
Make the script executable:

Bash

chmod +x ~/dotfiles/toggle_tweak.sh
3. Qtile Python Configuration (config.py)
Now we write the Python files that implement the visual and functional differences. You'll need to create two files, starting with a basic template.

A. Core Configuration (Common to Both Files)
You will need to import these at the top of both config.py files:

Python

from libqtile.config import Key, Screen, Group, Drag, Click
from libqtile.command import lazy
from libqtile import layout, bar, widget
from libqtile.widget import base
import os
import subprocess

# MOD KEY and Terminal
mod = "mod4"  # Windows/Super key
terminal = "gnome-terminal"
B. Sleek Mode Configuration (config-sleek/qtile/config.py)
This file is designed for Max Layout and a Minimal, Full-Width Bar.

Python

# LAYOUTS: Max Layout Focus
layouts = [
    layout.Max(
        border_focus='#81A1C1', # nord9 - Faded Royal Blue
        border_normal='#4C566A', # nord3
        margin=10,
    ),
    # Include MonadTall as an alternate, but Max is the default for sleekness
    layout.MonadTall(
        border_focus='#81A1C1',
        border_normal='#4C566A',
        margin=10,
        ratio=0.6 # A bit off-center
    ),
]

# BAR: Minimal Full-Width Bar (Segmented, Essential Info)
screens = [
    Screen(
        top=bar.Bar([
            widget.GroupBox(
                background="#2E3440", # nord0
                # Active/Inactive colors follow Nord scheme
                active="#81A1C1", # nord9
                inactive="#4C566A", # nord3
                highlight_method="block",
                # ... other minimalist GroupBox settings
            ),
            widget.CurrentLayout(foreground="#88C0D0"), # nord8
            widget.WindowName(foreground="#D8DEE9"), # nord4 (Text)
            widget.Spacer(),
            # Minimal Status Widgets
            widget.Clock(format="%I:%M %p | %a, %b %d", foreground="#D8DEE9"),
            widget.Systray(),
        ],
        size=24,
        background="#2E3440", # nord0
        opacity=0.9
        ),
    ),
]

# KEYBINDING for Toggle
keys = [
    # TWE Toggle: Super + T
    Key([mod], "t", lazy.spawn("~/dotfiles/toggle_tweak.sh"), desc="Toggle TWE/Sleek Mode"),
    # ... other standard keybindings like mod + Return to launch terminal
]
C. TWE Mode Configuration (config-twe/qtile/config.py)
This file is designed for MonadTall Layout and a High-Data Density Bar.

Python

# LAYOUTS: MonadTall Layout Focus
layouts = [
    layout.MonadTall(
        border_focus='#81A1C1', # nord9 - Faded Royal Blue
        border_normal='#4C566A', # nord3
        margin=5,
        ratio=0.55 # Slightly wider main window for productivity
    ),
    # Max layout included as an alternate
    layout.Max(
        border_focus='#81A1C1',
        border_normal='#4C566A',
        margin=5,
    ),
]

# BAR: High-Data Density Bar (Segmented, Productivity Info)
screens = [
    Screen(
        top=bar.Bar([
            widget.GroupBox(
                background="#2E3440", # nord0
                active="#81A1C1", # nord9
                inactive="#4C566A", # nord3
                highlight_method="block",
            ),
            widget.Prompt(),
            # High-Density Widgets
            widget.Net(
                interface="wlan0", # Adjust to your actual network interface
                format='{down}↓↑{up}',
                foreground="#A3BE8C", # nord14 (Green/Success)
            ),
            widget.Sep(),
            widget.CPU(
                format='CPU: {load_percent}%',
                foreground="#D08770" # nord12 (Orange/Warning)
            ),
            widget.Memory(
                format='Mem: {MemUsed:.0f}{mm}/{MemTotal:.0f}{mm}',
                foreground="#EBCB8B" # nord13 (Yellow/Data)
            ),
            widget.Spacer(),
            widget.WindowName(foreground="#D8DEE9"), # nord4
            widget.Clock(format="%I:%M:%S %p", foreground="#D8DEE9"),
            widget.Systray(),
        ],
        size=28,
        background="#2E3440", # nord0
        opacity=1.0 # Slightly less transparent to hold all the data
        ),
    ),
]

# KEYBINDING for Toggle
keys = [
    # TWE Toggle: Super + T
    Key([mod], "t", lazy.spawn("~/dotfiles/toggle_tweak.sh"), desc="Toggle TWE/Sleek Mode"),
    # ... other standard keybindings for window manipulation/resizing
]
