### To enable screen lock in case if "Super + L" not working
```
gsettings set org.gnome.desktop.lockdown disable-lock-screen 'false'
```


```
gsettings set org.gnome.desktop.wm.keybindings move-to-workspace-right "['<Super><Ctrl><Alt>Right']"
gsettings set org.gnome.desktop.wm.keybindings move-to-workspace-left "['<Super><Ctrl><Alt>Left']"
gsettings set org.gnome.desktop.wm.keybindings move-to-workspace-up "['<Super><Ctrl><Alt>Up']"
gsettings set org.gnome.desktop.wm.keybindings move-to-workspace-down "['<Super><Ctrl><Alt>Down']"

```
