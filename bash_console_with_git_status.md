## Setting bash prompt to show Git branch and it's status
* red - unsaved changes
* green - saved changes

### Define which config file is used for `PS1` variable
Usually it is one of the following:
* `~/.bashrc` - per user
* `/etc/bashrc` - global

### Modify `PS1`
This code will add Git branch name:
```
### For Git colouring
git_branch() {
     git branch 2> /dev/null | sed -e '/^[^*]/d' -e 's/* \(.*\)/\1/' | awk '{print " ["$1"]"}'
}

acolor() {
  [[ -n $(git status --porcelain=v2 2>/dev/null) ]] && echo 31 || echo 32
}
_GREEN="\[\033[32m\]"
_BLUE="\[\033[34m\]"
_YELLOW="\[\033[33m\]"
_BOLD_YELLOW="\[\033[01;33m\]"
_RED="\[\033[31m\]"
_RESET="\[\033[00m\]"

PS1="[${_GREEN}\u${_RESET}${_BOLD_YELLOW}@${_RESET}${_BLUE}\h${_RESET}] \W\[\033[\$(acolor)m\]\$(git_branch)\[\033[00m\] \$ "
### 
```

Open a new terminal to verify.

### Git alias
Create `~/.gitconfig` with content:
```
[user]
        name = Харитонова Ульяна Йорковна
        email = uliana.yorkowna@example.com
[alias]
        g = log --graph --oneline --decorate --all
```

### Set keys to move window between workspaces
```
gsettings set org.gnome.desktop.wm.keybindings move-to-workspace-down "['<Super><Ctrl><Alt>Down']"
gsettings set org.gnome.desktop.wm.keybindings move-to-workspace-up "['<Super><Ctrl><Alt>Up']"
gsettings set org.gnome.desktop.wm.keybindings move-to-workspace-left "['<Super><Ctrl><Alt>Left']"
gsettings set org.gnome.desktop.wm.keybindings move-to-workspace-right "['<Super><Ctrl><Alt>Right']"
```
