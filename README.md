# linux-bible
A collection of most commonly used Linux commands &amp; tools, articles, &amp; settings.


### 1. Change default screenshot directory:

> org/gnome/gnome-screenshot/auto-save-directory --> "file:///home/talha/Pictures/ScreenShots/"

>     gsettings set org.gnome.gnome-screenshot auto-save-directory "file:///home/$USER/Pictures/ScreenShots/"
 

### 2. Change ubuntu dock settings:

> https://linuxconfig.org/how-to-customize-dock-panel-on-ubuntu-20-04-focal-fossa-linux

> $ sudo apt install dconf-editor
$ gsettings set org.gnome.shell.extensions.dash-to-dock extend-height false
$ gsettings set org.gnome.shell.extensions.dash-to-dock dock-position BOTTOM
$ gsettings set org.gnome.shell.extensions.dash-to-dock transparency-mode FIXED
$ gsettings set org.gnome.shell.extensions.dash-to-dock dash-max-icon-size 64
$ gsettings set org.gnome.shell.extensions.dash-to-dock unity-backlit-items true


### 3. Add a new user:
> sudo adduser user

### 4. Add user to sudoers group:
> sudo usermod -a -G sudo user
> 
