# linux-bible
A collection of most commonly used Linux commands &amp; tools, articles, &amp; settings.


### 1. Change default screenshot directory:

> org/gnome/gnome-screenshot/auto-save-directory --> "file:///home/talha/Pictures/ScreenShots/"

>     gsettings set org.gnome.gnome-screenshot auto-save-directory "file:///home/$USER/Pictures/ScreenShots/"
 

### 2. Change ubuntu dock settings:

> https://linuxconfig.org/how-to-customize-dock-panel-on-ubuntu-20-04-focal-fossa-linux

> $ sudo apt install dconf-editor
> gsettings set org.gnome.shell.extensions.dash-to-dock extend-height false
> gsettings set org.gnome.shell.extensions.dash-to-dock dock-position BOTTOM
> gsettings set org.gnome.shell.extensions.dash-to-dock transparency-mode FIXED
> gsettings set org.gnome.shell.extensions.dash-to-dock dash-max-icon-size 64
> gsettings set org.gnome.shell.extensions.dash-to-dock unity-backlit-items true


### 3. Add a new user:
> sudo adduser user

### 4. Add user to sudoers group:
>     sudo usermod -a -G sudo user


### 5. Play MIDI (.mid) Files in Linux:
> sudo apt install vlc vlc-plugin-fluidsynth


### 6. My monitor only supports 1366x768. How to take 2K or 4K Screenshots?
>     cvt 3840 2160
>     # 3840x2160 59.98 Hz (CVT 8.29M9) hsync: 134.18 kHz; pclk: 712.75 MHz
>     # Modeline "3840x2160_60.00"  712.75  3840 4160 4576 5312  2160 2163 2168 2237 -hsync +vsync


eDP-1 is the Primary Monitor name, which can be found using `xrandr`  command.
>     xrandr --newmode "3840x2160_60.00"  712.75  3840 4160 4576 5312  2160 2163 2168 2237 -hsync +vsync
>     xrandr --addmode eDP-1 3840x2160_60.00

To use the new resolution:
>     xrandr -s 3840x2160_60.00


To take low compression, high quality screenshots, type:
>     scrot -q 100 -d 3


### 7. Find/Sort all files in the current dir WRT their size:
>     find . -type f -exec du -h {} \; | sort -h


Reverse the sort order with `-r` i.e. larger files first:
>     find . -type f -exec du -h {} \; | sort -hr


### 8. Find the size of all hidden files and directories (dot files & dirs) & sort them wrt size:
>     du -sh ./.* -c | sort -h


### 9. Kill all processes of current logged in user (equivalent to logout):
>     ps -fu $USER | awk '{print "kill -9 " $2}' | sh


