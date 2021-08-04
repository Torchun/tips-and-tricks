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
         git branch 2> /dev/null | sed -e '/^[^*]/d' -e 's/* \(.*\)/\1/' | awk '{print " "$1""}'
    }

    acolor() {
      [[ -n $(git status --porcelain=v2 2>/dev/null) ]] && echo 31 || echo 32
    }
    ### 

    [ "$PS1" = "\\s-\\v\\\$ " ] && PS1="[\u@\h \W]\033[\$(acolor)m\]\$(git_branch)\[\033[00m\] \$ "

```

Open a new terminal to verify.
