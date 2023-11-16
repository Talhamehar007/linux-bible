# linux-bible

A collection of most commonly used Linux commands &amp; tools, articles, &amp; settings.

## CHEAT SHEET:
[Linux Cheat Sheet](https://github.com/Talhamehar007/linux-bible/blob/main/src/cheatsheet-18.md)

### 1. Change default screenshot directory:

Edit this file using `dconfeditor`

```bash
org/gnome/gnome-screenshot/auto-save-directory --> "file:///home/talha/Pictures/ScreenShots/"
```

```bash
gsettings set org.gnome.gnome-screenshot auto-save-directory "file:///home/$USER/Pictures/ScreenShots/"
```

### 2. Change ubuntu dock settings:

[how-to-customize-dock-panel-on-ubuntu](https://linuxconfig.org/how-to-customize-dock-panel-on-ubuntu-20-04-focal-fossa-linux)

```bash
sudo apt install dconf-editor
gsettings set org.gnome.shell.extensions.dash-to-dock extend-height false
gsettings set org.gnome.shell.extensions.dash-to-dock dock-position BOTTOM
gsettings set org.gnome.shell.extensions.dash-to-dock transparency-mode FIXED
gsettings set org.gnome.shell.extensions.dash-to-dock dash-max-icon-size 64
gsettings set org.gnome.shell.extensions.dash-to-dock unity-backlit-items true
```

### 3. Add a new user:

```bash
sudo adduser user
```

### 4. Add user to sudoers group:

```bash
sudo usermod -a -G sudo user
```

### 5. Play MIDI (.mid) Files in Linux:

```bash
sudo apt install vlc vlc-plugin-fluidsynth
```

### 6. My monitor only supports 1366x768. How to take 2K or 4K Screenshots?

1st Run this command in your terminal: `cvt 3840 2160` for `4K` in this case (Change the Res Accordingly).

Then copy the output on 2nd line after Modeline.

Below is an exaple output


```bash
╭─talha@Manjaro in ~
 ╰─λ cvt 3840 2160
# 3840x2160 59.98 Hz (CVT 8.29M9) hsync: 134.18 kHz; pclk: 712.75 MHz
Modeline "3840x2160_60.00"  712.75  3840 4160 4576 5312  2160 2163 2168 2237 -hsync +vsync

 ╭─talha@Manjaro in ~ via  v3.10.9 took 48ms
 ╰─λ 
```

`eDP-1` is - in my - case - the Primary Monitor name, which can be found using `xrandr` command. Find yours by just typing `xrand` in your terminal...

Here's an example:

![2023-03-08_01-11](https://user-images.githubusercontent.com/57623612/223593467-eeb8b8a5-41e2-4086-938a-43eabc256c4f.png)

And then use these commands, replace `eDP-1` with your monitor name :

```bash
xrandr --newmode "3840x2160_60.00"  712.75  3840 4160 4576 5312  2160 2163 2168 2237 -hsync +vsync
xrandr --addmode eDP-1 3840x2160_60.00
```

To use the new resolution:

```bash
xrandr -s 3840x2160_60.00
```

To take low compression, high quality screenshots, type:

```bash
scrot -q 100 -d 3
 ```
 
### 7. Find/Sort all files in the current dir WRT their size:

```bash
find . -type f -exec du -h {} \; | sort -h
```

Reverse the sort order with `-r` i.e. larger files first:

```bash
find . -type f -exec du -h {} \; | sort -hr
```

### 8. Find the size of all hidden files and directories (dot files & dirs) & sort them wrt size:

Dot directories are usually found in the $HOME `(~)` folder and are hidden by default...

```bash
find . -maxdepth 1 -type d  -iname ".*" -exec du -sh {} \;  | sort -h
```

### 9. Kill all processes of current logged in user (equivalent to logout):

```bash
ps -fu $USER | awk '{print "kill -9 " $2}' | sh
```

OR

```bash
killall -u $USER
```

### 10. Create & Extract `tar` Files:

To create a simple tar file from a given dir use:

```bash
tar -cf Pictures.tar Pictures/
```

To extract a tar file:

```bash
tar -xf Pictures.tar
```

For verbose output, use `-v` option:

```bash
tar -cvf Pictures.tar Pictures/
```

```bash
tar -xvf Pictures.tar
```

- `-c` : create
- `-x` : Extract (the oposite of create)
- `-f` : specify a filename (comes always at the end)
- `-v` : verbose output
- `-z` : compress with `gzip` (Create `.tar.gz` files
- `-j` : compress with `bzip2` (Create `.tar.gz2` files)
- `-J` : compress with `xz` (Create `.tar.xz` files)
- `-t` : list contents of archive

To create a .tar.gz file:

```bash
tar -czf Pictures.tar.gz Pictures/
```

To extract a .tar.gz file:

```bash
tar -xzf Pictures.tar.gz Pictures/
```

To create a .tar.gz2 file:

```bash
tar -cjf Pictures.tar.gz2 Pictures/
```

To extract a .tar.gz file:

```bash
tar -xjf Pictures.tar.gz2 Pictures/
```

To create a .tar.xz file:

```bash
tar -cJf Pictures.tar.xz Pictures/
```

To extract a .tar.xz file:

```bash
tar -xJf Pictures.tar.xz Pictures/
```

Use Multi-Threading to Creacte/Extract tarballs:

To use all the resourses to speed up the process we can (in BASH):

```bash
XZ_DEFAULTS="--threads=4"; export XZ_DEFAULTS;

tar -cJvf archive.tar.xz Pictures/
```

or in ZSH:

```bash
set XZ_DEFAULTS "--threads=4"; export XZ_DEFAULTS;
```

Or we can provide `-I, --use-compress-program=PROG` option to use a given compression program instead of default gzip, bzip2 or xz programs:

`pigz`, `pbzip2` & `pxz` are the `PARALLEL` Implementaions of the `gzip`, `pbzip2`, & `xz` respectively.

These can be installed using apt in Ubuntu:

```bash
sudo apt install pigz pbzip2 pxz
```

Which are just the PARALLEL implementations of the given programs (Parallel Implementation of gzip, pbzip2, & xz)

If you're using Ubuntu 20.04, the pxz program is not available in default repos.
So you can add bionic (Ubuntu 18.04) main universe repo to install this program:

```bash
sudo apt edit-sources
```

And then add this line at the end of the file:

`deb http://cz.archive.ubuntu.com/ubuntu bionic main universe`

Then install the `pxz` program:

```bash
sudo apt install pxz
```

To use these programs instead of defaults we can provode flags like this:

```bash
tar -I pxz -cvf Pictures.tar.xz Pictures/
tar -I pigz Pictures.tar.gz Pictrures/
tar -I pbzip2 Pictures.tar.gz2 Pictrures/
```

Or even we can provide default gz option with additional flags:
`-9` means compression level 9 (maximum compression)

```bash
tar -c -I 'xz -9' -f archive.tar.xz Dir/
```


`-T0` means to use all the available threads (I Tried but it does not work, so instead `pxz` is recomended)

```bash
tar -c -I 'xz -9 -T0' -f archive.tar.xz Dir/
```

One-liner to create a .tar.xz file with 4 Threads to speed up:

```bash
XZ_DEFAULTS="--threads=4"; export XZ_DEFAULTS; tar -cJf Pictures.tar.xz Pictures/
```

OR:

```bash
tar -I pxz -cf Pictures.tar.xz Pictures/
```

### 11. SSH - Get ssh connected ports information

From Server:

```bash
sudo lsof -i -n | egrep '\<sshd\>'
```

From client:

```bash
sudo lsof -i -n | egrep '\<ssh\>'
```

### 12. Find the USED and UNSED space using `lsblk`:

```bash
sudo lsblk -o NAME,SIZE,FSTYPE,MOUNTPOINT,FSUSED,FSUSE%,FSAVAIL /dev/sda /dev/sdb
```
