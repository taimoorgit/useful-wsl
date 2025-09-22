# useful-wsl

Random WSL knowledge I want to share/retain.

<img width="1115" height="628" alt="image" src="https://github.com/user-attachments/assets/0c1e5763-f565-4ea3-b4b1-75f567f7e6ce" />

## My setup

* OS: Windows 11 Pro
* Windows Features:
  * Hyper-V
  * Virtual Machine Platform
  * Windows Subsystem for Linux
* Ensure that in task manager, your processor shows `Virtualization: Enabled` (this means you have the Intel/AMD virtualization extension enabled in BIOS)
* Editor: Visual Studio Code
  * Installed using [Ninite](ninite.com)
  * To open folders, I simply use `cd $HOME/projects/abc && code .`
  * For that to work, you need the WSL extension from Microsoft, it should autodetect your WSL and set things up accordingly.
* WSL Distro: Ubuntu

## Recommended bashrc/zshrc

```
# windows related aliases
export WINHOME="/mnt/c/Users/Taimoor"
export WINSS="/mnt/c/Users/Taimoor/Pictures/Screenshots"

# copy over the latest screenshot from Windows to current working directory
function winss {
  local n="${1:-1}"
  ls -t "$WINSS" | head -"$n" | while read -r file; do
    new_name=$(echo "$file" | sed -E 's/Screenshot ([0-9-]+) ([0-9]+)\.([a-z]+)/ss-\1-\2.\3/')
    echo "copying '$file' as '$new_name' ..."
    cp "$WINSS/$file" "./$new_name"
  done
}

# copy over a file/folder to your Windows desktop
function winsd {
  if [ -z "${1:-}" ]; then
    echo "no file/folder selected to send to desktop"
  fi
  echo "copying $1 to $WINHOME/Desktop ..."
  cp -R "$1" "$WINHOME/Desktop"
}
```

## Tip - Shortcut to copy to Windows desktop from WSL

Use the functions and aliases I wrote, then

```
$ winsd linter.py
copying linter.py to /mnt/c/Users/Taimoor/Desktop ...
```

## Tip - How to paste Windows screenshots to Claude Code running in WSL

If you want to use snipping tool to take a screen, and then have Claude Code be able to see them, you'll find that nothing gets pasted.

To get around this, use my bashrc/zshrc additions from above.

```
# get latest screenshot taken from snipping tool
$ winss
copying 'Screenshot 2025-09-22 131048.png' as 'ss-2025-09-22-131048.png' ...

# now in claude code, type "look at @ss-2025-09-22-131048.png"

# if claude code has your aliases, you could also just do "! winss"
```

## Tip - pipe output of a command from WSL to Windows clipboard

```
# in WSL
$ cat README.md | clip.exe

# find out more about the clip command
$ clip.exe "/?"

CLIP

Description:
    Redirects output of command line tools to the Windows clipboard.
    This text output can then be pasted into other programs.

Parameter List:
    /?                  Displays this help message.

Examples:
    DIR | CLIP          Places a copy of the current directory
                        listing into the Windows clipboard.

    CLIP < README.TXT   Places a copy of the text from readme.txt
                        on to the Windows clipboard.
```

## Tip - using `find` command

```
# always prefix with an ignore clause for /mnt. This is so that it doesn't try to look at my Windows drives, which can take forever
find / -path /mnt -prune -o \
  -name 'python3*' \ -type f -executable -print 2>/dev/null
```
