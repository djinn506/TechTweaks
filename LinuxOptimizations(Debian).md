# Linux Optimizations for Debian based distros

Results: POP! OS Current Memory use: under 760M.
Edit: now memory is around 1G, but that's due to the changes to gnome that's still the lowest I can get it. [Since 22.04 it's now 1.00GB.]

------
Menu

[Basic Optimizations](#basic-optimizations)





# Basic Optimizations
## * Disable Animations using gnome-tweaks

_reason: saves on computation time_

```bash
sudo apt-get install gnome-tweaks
```

gnome-tweaks >> disable animations

## * Set Font Antialiasing to "Standard (grayscale)"

_reason: cheaper in my opinion_

## * Install Preloader adaptive readahead daemon

_reason: Monitors commonly used programs and preloads them in memory to speed up openning apps_
<https://manpages.ubuntu.com/manpages/bionic/man8/preload.8.html>

```bash
preload -n 0
```

**optional** // this code sets preload niceness at normal (preloader it's normally more agressive)

## * Uninstall unneeded fonts (?) optional fonts, make sure to edit the following to your liking before removing

_reason: All fonts load on RAM, so you can free up a lot of RAM just disabling them. And you're probably not going to use any other language._

```bash
sudo apt-get remove language-pack-ar language-pack-de language-pack-es language-pack-fr language-pack-gnome-ar language-pack-gnome-de language-pack-gnome-fr language-pack-gnome-es language-pack-gnome-it language-pack-gnome-ja language-pack-gnome-pt language-pack-gnome-ru language-pack-gnome-zh-hans language-pack-gnome-zh-hant
```

## * Disable most fonts, using "Font Manager"

_reason: all fonts load on RAM, so you can free up a lot of RAM just disabling them_

Pop! OS minimal required fonts: Fira Sans and Roboto Slab, Deja Vu Mono for console
    //Edit: only needed DejaVu Sans and Liberation Mono
Manjaro xfce: only needed Noto Sans / Noto Sans Mono

Minimal Tested Working FONTS:
    Liberation Mono (set as console font in gnome-tweaks (Monospace))
    DejaVu Sans (needed for bashtop braille characters)
    Fira Sans (Regular POP)
    Roboto Slab (Regular POP, set for everything else in gnome-tweaks)

## * Edit Swappiness on POP:

_reason: swappiness handles how often RAM data gets moved over to the swap partition. So less moving results in a more responsive system, as long as you have enough RAM avaliable_

```bash
sudo gedit /etc/sysctl.conf // This works on POP OS
```
Then add these lines:

```bash
vm.swappiness=10
vm.vfs_cache_pressure=50
```

## * Edit Boot Options for SSDs:

_reason: SSDs in particular don't need as many flags to be enabled as HDDs, skipping this data makes data easier to access_

From: <https://wiki.debian.org/SSDOptimization#Low-Latency_IO-Scheduler>

1. Edit the fstab file:

    ```bash
    sudo gedit /etc/fstab/
    ```

2. Add "nodiratime" and "noatime" before "errors=remount-ro":

    ```bash
    example: "noatime,nodiratime,errors=remount-ro" //skips writting too many timestamps on files.
    ```

Other(NOT working for me):

from: <https://haydenjames.io/increase-performance-lifespan-ssds-sd-cards/>

    ```bash
    sudo gedit /etc/fstab
    ssd,compress 0 0
    ```

## * Enable trim on SSDs
_reason: it expands SSDs lifetime so it's a bit of a no-brainer_

```bash
sudo apt-get install util-linux
sudo systemctl enable fstrim.service
sudo systemctl enable fstrim.timer
sudo systemctl start fstrim.service
sudo systemctl start fstrim.timer
```

## **optional** Disable Mitigations

_reasons: unless you're running a server, chances are you'll never come across viruses abusing your linux system. And you'll get a big performance boost disabling it_

From: "Speedup Linux" - <https://christitus.com/speedup-linux/>

..."Linux by default is meant for servers and actually decreases the performance for greater security. While this great in business, when using Linux as a desktop it is not ideal unless your are serving other devices on your network with that machine.

This will have a substantial increase in performance just by doing disable many mitigations that happen in multi-threaded systems. The more core count you have the greater the performance gain. Some performances increases can be as large as 30%, but the average increase is about 10%.

1. Add this to your /etc/default/grub under line GRUB_CMDLINE_LINUX="rhgb quiet":

```bash
GRUB_CMDLINE_LINUX="rhgb quiet mitigations=off"
```

2. Install Grub Customizer

3. Run Grub Customizer and set kernel parameters: quiet splash mitigations=off

## **optional** Purge Libreoffice

_reason: If you don't use docs, excel sheets, etc.. you don't need this program, and by removing it you'll get rid of it's fonts_

```bash
sudo apt-get remove --purge libreoffice*
sudo apt-get clean
sudo apt-get autoremove
```

# Custom Profile and Aliases

Aliases are custom commands that can be set per user that allows short scripts to be run on the terminal when you type in the corresponding aliases in the terminal.

Depending on your distro you can add aliases to "~/.profile" or "~/.bashrc_aliases"

1. Create the file ".bash_aliases"

    ```bash
    touch ~/.bash_aliases >> .bash_aliases
    ```
2. Edit it's contents using gedit

    ```bash
    gedit ~/.profile
    ```

## Disable Touch on wacom tablets

_reason: I don't use the touch functions on my wacom and it bothers me_

```bash
alias notouch= xsetwacom set "$( xsetwacom --list | awk '/TOUCH/ {print $8}' )" touch off
```

(gets wacom touch id and sets it to off)

## Kill gracefully "touchegg" service on startup

_reason: I don't use touch screens on my system, it doesn't affect wacom stylus)_

Add to "~/.bashrc" so it runs whenever you open the terminal

```bash
pkill -15 touchegg
```

## Open VM in virt-viewer

_reason: Running virt-viewer allows to share folders between VMs, while using the "spice guest additions"_


```bash
alias runwin='virt-viewer --connect=qemu:///system --domain-name win10vm'
```

## Update and Upgrade

_reason: runs update, upgrade and also updates flatpaks_

```bash
alias popupdate='sudo apt-get update && sudo apt-get upgrade; flatpak update;'
```

## Autoremove and Autoclean

_reason: runs autoremove and autoclean, removes old installation files_

```bash
alias popclean='sudo apt-get autoremove && sudo apt-get autoclean'
```

## Mount Drive

_reason: mounts <DRIVENAME> in /media/j/<DRIVENAME>_

```bash
alias mountDrive='sudo mkdir -p /media/j/<DRIVENAME>/ && sudo mount /dev/sdc2 /media/j/<DRIVENAME>/;'
```
## Unmount Drive

_reason: unmounts <DRIVENAME>_

```bash
alias umountDrive='sudo umount /media/j/<DRIVENAME>/ && sudo rm -d /media/j/<DRIVENAME>'
```
## Sync Folders

_reason: mirrors data from /Docs/blender to /media/j/<DRIVENAME>/blender_

```bash
alias blendersync='rsync -arvP ~/Documents/blender/ /media/j/<DRIVENAME>/blender/'
```
## Pomodoro

_reason: runs pomodoro clock_

```bash
alias pomodoro='echo "Pomodoro START"; sleep 1500; echo "REST!"; sleep 300; echo "Rest OVER";'
```

## Disable Evolution

_reason: if you don't use it as a mail application, then you dont need it in Ubuntu.. but the system uses it for notifications_

You can set it in Stacer >> Add Startapp with:

```bash
pkill -15 evolution
```

## Update Aliases
_if you want to update aliases while in the terminal_
```bash
sources ~/.bashrc
```

or

```bash
. ~/.bashrc
```

## Disable PopShop 
_reason: it takes up some RAM, so it's always open in the background_

1. Startup Applications > Disable Pop Shop (Pop!_OS Release Check)

2. Add these lines in your ~/.bashrc
```bash
pkill -15 io.element
```

## Disable Touchegg
_reason: used for fingertouch and gestures on touch screens, but I don't use touch screens on my system_

```bash
cd /etc/xdg/autostart/
mv touchegg.desktop touchegg.desktop.hold
```
This code renames the touchegg.desktop file so it can't be lauched anymore, and you can always revert it by renaming it back.
# Security

## * Disable SSH

To do this, head to “/etc/ssh/sshd_config” to access the file responsible for configuring SSH. Then open it with a text editor and remove root login by entering the “#PermitRootLogin no” command into the console.

## * Wired Settings:
    Set Clone Adress to AA:BB:CC:DD:EE:DD
    Set IPV4 to Automatic DHCP and set DNS
    Set IPV6 to Automatic DHCP ONLY and set DNS

```bash
Cloudflare DNS
IPv4
1.1.1.1,1.0.0.1
IPv6
2606:4700:4700::1111,2606:4700:4700::1001
Google DNS
IPV4
8.8.8.8,8.8.4.4
IPV6
2001:4860:4860::8888,2001:4860:4860::8844
```

## * Disable SSH service

```bash
sudo systemctl stop ssh
sudo systemctl disable ssh
```

## * Disable backports updates (In the updating options)

## * Disable multiverse updates (In the updating options)

## * Install Apparmor
(Already installed in PopOS)
_reason: sandboxes applications so data doesn't leak_

```bash
sudo apt-get install apparmor-profiles apparmor-utils
sudo aa-enforce /etc/apparmor.d/*
sudo systemctl enable apparmor.service
sudo systemctl start apparmor.service
```

You can also check apparmor status:

```bash
sudo systemctl status apparmor.service
```

<https://ubuntu.com/server/docs/security-apparmor>

## * Firewall - Hardening

<https://askubuntu.com/questions/339507/how-can-i-use-firefox-with-ubuntu-firewall-gufw>

port 80 for HTTP protocol
port 443 for HTTPS protocol

```bash
sudo default deny incoming  
sudo default deny outgoing
```

internet

```bash
sudo ufw allow out 53  
sudo ufw allow out http  
sudo ufw allow out https  
sudo ufw allow out 8080 
```

thunderbird

```bash
sudo ufw allow proto tcp from 173.194.0.0/16   
sudo ufw allow out to 173.194.0.0/16 
sudo ufw allow out to 74.125.0.0/16 port 465 proto tcp
```

hotmail

```bash
sudo ufw allow proto tcp from 207.46.0.0/16   
sudo ufw allow out to 207.46.0.0/16 port    
sudo ufw allow out to 65.52.0.0/14 port 587 proto tcp
```

## * Firewall Advanced

<https://www.youtube.com/watch?v=ZhMw53Ud2tY>
<https://www.youtube.com/watch?v=2IosbILbMWQ>
<https://null-byte.wonderhowto.com/how-to/locking-down-linux-using-ubuntu-as-your-primary-os-part-2-network-attack-defense-0185709>

```bash
sudo systemctl disable cups-browsed
sudo systemctl disable avahi-daemon
sudo ufw default deny incoming
sudo ufw default deny forward
sudo ufw default deny outgoing
ifconfig -a
```

# * Useful Linux Commands

Update and Upgrade to newer Distro version
```bash
sudo apt-get update
sudo apt-get dist-upgrade
```

Display Network Connections
```bash
ifconfig
```

Check Freespace
```bash
df -ah
```

Old version to check service status
```bash
service ServiceName status
```

New version to check service status
```bash
systemctl status ServiceName
```

Display files and directories in current
```bash
ls
```

Display Size of Directory
```bash
du -sh FolderName
```

Display Network Connections
```bash
netstat -tulpn
```

Display Running Processes
```bash
ps aux | grep ProgramName
```
or
```bash
top
```

Mount volumes
```bash
mount /dev/sda2 /mnt
```

Display Mounted Volumes
```bash
mount
```

Display Working Directory / path
```bash
pwd
```

Display all installed packages
```bash
dpkg --list
```

Display Kernel Version
```bash
uname -r
```

Remove Old Kernels
```bash
dpkg --list | egrep -i --color 'linux-image|linux-headers|linux-modules' | awk '{ print $2 }'
```

Block Analytics

1. Edit Hosts file
    ```bash
    gedit /etc/hosts
    ```
2. Add these lines:
    ```bash
    **Google Analytics**
    0.0.0.0 www.google-analytics.com
    0.0.0.0 172.217.7.14
    **WACOM Analytics**
    0.0.0.0 link.wacom.com/analytics/analytics.xml
    0.0.0.0 link.wacom.com
    ```

* Fix Broken Packages on Ubuntu Distros

```bash
apt-get remove apt-utils
sudo apt --fix-broken install
sudo dpkg -r --force-all apt-utils // works!
```

```bash
sudo apt-get install -f
    -or-
sudo apt-get -f remove
```

```bash
sudo apt-get install aptitude
sudo aptitude install **package-name**
```

```bash
sudo apt-get update
sudo apt-get clean
sudo apt-get autoremove
```

```bash
sudo apt --fix-broken install
sudo apt-get update && sudo apt-get upgrade
sudo dpkg --configure -a
sudo apt-get install -f
sudo apt-get install **package-name**
```

## Enable zram and zswap
Not really needed
    <https://www.youtube.com/watch?v=RGVt16xiERc>

Zswap

```bash
cat /sys/module/zswap/parameters/enabled //returns boolean
sudo -i
echo 1 > /sys/module/zswap/parameters/enabled
cat /sys/module/zswap/parameters/enabled
nano /etc/default/grub
"grub_cmdline_linux_default= (add) zswap.enabled=1"
```

<https://www.youtube.com/watch?v=XAJoszyzXPs>
<https://www.addictivetips.com/ubuntu-linux-tips/enable-zswap-on-linux>
<https://ubuntu-mate.community/t/enable-zswap-to-increase-performance/11302>

## Enable TMPFS 
Writes tmp directly to RAM memory instead of disk

```bash
sudo cp -v /usr/share/systemd/tmp.mount /etc/systemd/system/ 
sudo systemctl enable tmp.mount
**restart machine**
sudo systemctl status tmp.mount
```
## Install Profile Sync Daemon
psd - Sets browser profiles to run on ram instead, also needs to Enable OverlayFS.

```bash
sudo apt-get install psd
sudo systemctl enable psd
sudo systemctl start psd
```

## PulseAudio

```bash
alsamixer // front mic boost
sudo alsactl store // stores alsamixer configuration?
```

## youtube-dl

```bash
-x --audio-format mp3
youtube-dl -f 22 -c -i --external-downloader-args '-n 10' --external-downloader /usr/bin/axel https://www.youtube.com/watch?v=yNj5vFEsp74
youtube-dl -x --audio-format mp3 --external-downloader-args '-n 10' --external-downloader /usr/bin/axel https://www.youtube.com/watch?v=_iQ7f9vV7cs
-f bestvideo+bestaudio --merge-output-format mkv
youtube-dl --external-downloader-args '-n 10' --external-downloader /usr/bin/axel https://www.youtube.com/watch?v=abjRQeYmHCo
```

Download reddit "hot" files: // requires jq and curl

```bash
curl -sS -H "User-Agent: TikTok Bot 1.0" https://www.reddit.com/r/REDDIT/hot.json?limit=10 | jq -r '.data.children[].data.url_overridden_by_dest' | youtube-dl -qiw --no-warnings -a -
ffplay <mp3_file>
```

Terminal Autocomplete // not needed anymore, this is by default on POP
    <https://unix.stackexchange.com/questions/55203/bash-autocomplete-first-list-files-then-cycle-through-them/55632>

```bash
bind "TAB:complete"; bind "set show-all-if-ambiguous on"
bind "TAB:complete"; bind "set show-all-if-ambiguous off"
```

// make tab cycle through commands after listing // not really needed

```bash
bind '"\t":menu-complete'
bind "set show-all-if-ambiguous on"
bind "set completion-ignore-case on"
bind "set menu-complete-display-prefix on"
```

Microphone Noise Fix // Not Needed anymore
    <https://www.youtube.com/watch?v=lTodCeVAfpI>

```bash
sudo nano /etc/pulse/default.pa
load-module module-echo-cancel source_name=noechosource sink_name=noechosink
// ctrl+O to save
// ctrl+X to exit
pulseaudio -k
```

One Time Echo Microphone Fix

```bash
pactl load-module module-echo-cancel source_name=noechosource sink_name=noechosink
// and once your recording is done, you can turn it back off, with
pactl unload-module module-echo-cancel
```

Latest working is just using "PulseEffects" and setting the NoiseFilter, Gate and Compressor on Input(Mic) // but lowers recording quality. The same package for wayland is called "Easy Effects"

## WEBP

```bash
sudo apt install webp -y
dwebp file.webp -o file.png
find ./ -name "*.webp" -exec dwebp {} -o {}.png \;
```

Converts webp to png

```bash
ffmpeg -i file.webp out.png
```

Converts ANY webp to jpg

```bash
for x in ls *.webp; do  ffmpeg -i $x ${x%.webp}.jpg;
```

**optional** Services Enabled on Stacer:
- accounts-daemon
- acpid
- apparmor
- avahi-daemon
- blk-availability
- bluetooth (gotta disable it)
- brltty (gotta disable it)
- com.system76.Scheduler
- console-setup
- cron
- cups-browsed (gotta disable it) // Printer on network
- cups // Local Printer
- dmesg
- e2scrub_reap
- finalrd
- irqbalance
- keyboard-setup
- libvirt-guests
- libbirtd
- lm-sensors
- lvm2-monitor
- ModemManager
- network-dispatcher
- networking
- NetworkManager-dispatcher
- NetworkManager-wait-online
- NetworkManager
- nvidia-persistenced
- openvpn //disable it
- pop-upgrade //disable it
- qemu-kvm
- resolvconf
- rsync
- rsynclog
- rtkit-daemon
- secureboot-db
- setvtrgb
- speech-dispatcherd // disable it
- switcheroo-control
- systemd-networkd
- systemd-pstore
- systemd-resolved
- systemd-sysext
- touchegg //disable it
- ua-reboot-cmds
- udisks2
- ufw
- upower
- wpa_supplicant

**optional** Disable services that cannot be disabled by stacer

//must be done with systemctl

- touchegg
- bluetooth
- brltty
- pop-upgrade
- openvpn

## Disable Touchegg:

```bash
cd /etc/xdg/autostart/
mv touchegg.desktop touchegg.desktop.hold
```

## Disable Bluetooth:

```bash
nano /etc/bluetooth/main.conf 
set AutoEnable=false
systemctl disable bluetooth
```

## Disable Braille Device Support
// make sure you don't use it

```bash
sytemctl disable brltty
```

## Disable OpenVPN

```bash
systemctl disable openvpn
```

## Remove Evolution 
This removes default mail client (it's also used for system notifications)

```bash
sudo apt-get remove evolution
sudo apt-get autoclean
```

ADDENDUM

* nautilus-typeahead
    Allows to navigate the files by typing the filenames instead of jumping to search
    This is far quicker cause in time you get in the mechanical habit
    <https://launchpad.net/~lubomir-brindza/+archive/ubuntu/nautilus-typeahead>

* libjpeg -turbo
    Very useful for dealing with large datasets of images, for instance when working with photogrametry. Loads images and their thumbnails very quick.
    I wouldn't recomend unless working with 400 images it's not worth the trouble.
    <https://www.libjpeg-turbo.org/>
    //Already being used in most distros
