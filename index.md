

Disclaimer: 
	Make a backup of your files before attempting anything. This is inteded for desktop use, not server. Some linux knowledge is required to use this, all code snippets are meant to be typed into the terminal, links are not hyperlinked since it's just there in case you need more info. Reach out if you found this useful or you want anything to be included here as well. Have fun!

Pop! OS Current Memory use: under 760M // Edit: now memory is around 1G(that includes Preloader's cache), but that's likely due to the changes to gnome extensions that's still the lowest I can get it til I use the lateset version of Pop! OS.

System and apps boot quickly, system is very responsive even with gnome, apps are running on ram instead of the drive, firefox also which can be a little tricky. Cpu load is very low, but admitedly I am using a lot more ram than necesary just for everything to be as fast as it gets, you don't have to use tmps and psd daemon if that's not to your liking.

Kernels: I'm using the regularly updated Pop! OS linux kernel, they update it sooner than ubuntu. Had good experiences in the past using liquorix kernels too, but I don't seem to need it with this current configuration.

Future endeavors: make a script to set all these settings automatically, test all of this on Arch/Manjaro hardware using xfce for the lowest ram footprint.

 START HERE

Disable Animations using gnome-tweaks

Set Font Antialiasing to "Standard (grayscale)" // cheaper imo

Install Preloader Adaptive Readahead Daemon
	Monitors commonly used programs and preloads them in memory to speed up openning apps
	https://manpages.ubuntu.com/manpages/bionic/man8/preload.8.html
```	
preload -n 0
```
// this code sets preload niceness at normal, this helps not hanging your cpu priority
	
Uninstall unneeded fonts (?) optional fonts, make sure to edit to your liking before removing.
```	
sudo apt-get remove language-pack-ar language-pack-de language-pack-es language-pack-fr language-pack-gnome-ar language-pack-gnome-de language-pack-gnome-fr language-pack-gnome-es language-pack-gnome-it language-pack-gnome-ja language-pack-gnome-pt language-pack-gnome-ru language-pack-gnome-zh-hans language-pack-gnome-zh-hant
```

Disable most fonts, using "Font Manager"
	Pop! OS minimal required fonts: Fira Sans and Roboto Slab
	Deja Vu Mono for console
	//Edit: only needed DejaVu Sans and Liberation Mono
	Manjaro xfce: only needed Noto Sans / Noto Sans Mono

Minimal Tested Working FONTS: 
	Liberation Mono (set as console font in gnome-tweaks (Monospace))
	DejaVu Sans (needed for bashtop braille characters)
	Fira Sans (Regular POP)
	Roboto Slab (Regular POP, set for everything else in gnome-tweaks)
	
Edit Swappiness on POP:
```
sudo gedit /etc/sysctl.conf // This works on POP OS
```
```
vm.swappiness=10
vm.vfs_cache_pressure=50
```

Edit Boot Options for SSDs:
```
sudo gedit /etc/fstab/
```
add "nodiratime" and "noatime" before "errors=remount-ro" // also "discard"
ex: "noatime,nodiratime,errors=remount-ro" //skips writting too many timestamps on files.  // This works atm

Other(NOT working for me):
https://haydenjames.io/increase-performance-lifespan-ssds-sd-cards/

```
sudo gedit /etc/fstab
ssd,compress 0 0
```

https://wiki.debian.org/SSDOptimization#Low-Latency_IO-Scheduler

Enable trim on SSDs

```
sudo apt-get install util-linux
sudo systemctl enable fstrim.service
sudo systemctl enable fstrim.timer
sudo systemctl start fstrim.service
sudo systemctl start fstrim.timer
```

Purge Libreoffice

```
sudo apt-get remove --purge libreoffice*
sudo apt-get clean
sudo apt-get autoremove
```


Custom Profile and Aliases

```
alias >> ~/.profile
```

Disables Touch on wacom tablets
```
xsetwacom set `xsetwacom --list | awk '/TOUCH/ {print $8}'` touch off
```

Kill gracefully touchegg service on startup (touch only, doesn't affect wacom stylus)
```
pkill -15 touchegg // doesn't work ? maybe pipe it into kill -15
```

		
```
touch ~/.bash_aliases >> .bash_aliases
```

```
alias runwin='virt-viewer --connect=qemu:///system --domain-name win10vm'
```
```
alias popupdate='sudo apt-get update && sudo apt-get upgrade; flatpak update;'
```

```
alias popclean='sudo apt-get autoremove && sudo apt-get autoclean'
```

```
alias mount<DRIVENAME>='sudo mkdir -p /media/~/<DRIVENAME> && sudo mount /dev/sdc2 /media/~/<DRIVENAME>;'
```

```
alias umount<DRIVENAME>='sudo umount /media/~/<DRIVENAME>/ && sudo rm -d /media/~/<DRIVENAME>'
```

```
alias blendersync='rsync -arvP ~/Documents/blender/ /media/~/<DRIVENAME>/blender/'
```

```
alias pomodoro='echo "Pomodoro START"; sleep 1500; echo "REST!"; sleep 300; echo "Rest OVER";'
```

Disable Evolution: completely unneded in Ubuntu.. but the system uses it for notifications
In Stacer, Add Startapp with:
```	
pkill -15 evolution
```

Force Update Aliases
```
sources ~/.bashrc
```
```
. ~/.bashrc // updates bash sources
``` 
Disable PopShop // takes some resources
Startup Applications > Disable Pop Shop (Pop!_OS Release Check) // S76 priority balance relies on PopShop as far as I know in order to work properly
```
pkill -15 io.element
```
Disable Touchegg:  // used for fingertouch and gestures
```
/etc/xdg/autostart/touchegg.desktop
// then rename to touchegg.desktop.hold
```
	
Security

Disable SSH
	To do this, head to “/etc/ssh/sshd_config” to access the file responsible for configuring SSH. Then open it with a text editor and remove root login by entering the “#PermitRootLogin no” command into the console.

Wired Settings:
	Set Clone Adress to AA:BB:CC:DD:EE:DD
	Set IPV4 to Automatic DHCP and set DNS
	Set IPV6 to Automatic DHCP ONLY and set DNS
```
//Cloudflare DNS
IPv4
1.1.1.1,1.0.0.1
IPv6
2606:4700:4700::1111,2606:4700:4700::1001
// Google DNS
IPV4
8.8.8.8,8.8.4.4
IPV6
2001:4860:4860::8888,2001:4860:4860::8844
```
Disable SSH service
```
sudo systemctl stop ssh
sudo systemctl disable ssh
```

Disable backports updates (In the updating options)
Disable multiverse updates (In the updating options)

Install Apparmor // Already installed in PopOS
```
sudo apt-get install apparmor-profiles apparmor-utils
sudo aa-enforce /etc/apparmor.d/*
sudo systemctl enable apparmor.service
sudo systemctl start apparmor.service
```
		
https://ubuntu.com/server/docs/security-apparmor
	
Firewall - Hardening

https://askubuntu.com/questions/339507/how-can-i-use-firefox-with-ubuntu-firewall-gufw

port 80 for HTTP protocol
port 443 for HTTPS protocol

```
sudo default deny incoming  
sudo default deny outgoing
```

internet
```
sudo ufw allow out 53  
sudo ufw allow out http  
sudo ufw allow out https  
sudo ufw allow out 8080 
```

thunderbird
```
sudo ufw allow proto tcp from 173.194.0.0/16   
sudo ufw allow out to 173.194.0.0/16 
sudo ufw allow out to 74.125.0.0/16 port 465 proto tcp
```

hotmail
```
sudo ufw allow proto tcp from 207.46.0.0/16   
sudo ufw allow out to 207.46.0.0/16 port    
sudo ufw allow out to 65.52.0.0/14 port 587 proto tcp
```

Firewall Adv

https://www.youtube.com/watch?v=ZhMw53Ud2tY
https://www.youtube.com/watch?v=2IosbILbMWQ
https://null-byte.wonderhowto.com/how-to/locking-down-linux-using-ubuntu-as-your-primary-os-part-2-network-attack-defense-0185709/
	
```
sudo systemctl disable cups-browsed
sudo systemctl disable avahi-daemon
sudo ufw default deny incoming
sudo ufw default deny forward
sudo ufw default deny outgoing
ifconfig -a
```

Hosts Hardening
https://github.com/StevenBlack/hosts

Firefox Hardening
https://github.com/arkenfox/user.js

Useful Linux Commands

```
sudo apt-get update
sudo apt-get dist-upgrade

ifconfig // network configurations

df -ah // filesystems

service <service name> status // service status old

systemctl status <service name> // service status new

ls

du -sh <foldername> // size of folder

netstat -tulpn // network connections

ps aux | grep <service name> // processes running
top // same

ls /mnt // whats on /mnt folder
mount /dev/sda2 /mnt // mounting volumes
mount // check mounted volumes

pwd // print working directory // filepath

dpkg --list // lists all installed packages

uname -r // outputs kernel version
```
Remove Old Kernels
```
dpkg --list | egrep -i --color 'linux-image|linux-headers|linux-modules' | awk '{ print $2 }'
```

Block Analytics

```
gedit /etc/hosts
```
```
#Google Analytics
0.0.0.0 www.google-analytics.com
0.0.0.0 172.217.7.14
#WACOM Analytics
0.0.0.0 link.wacom.com/analytics/analytics.xml
0.0.0.0 link.wacom.com
```

FIX BROKEN PACKAGES on Ubuntu Distros

```
apt-get remove apt-utils
sudo apt --fix-broken install
sudo dpkg -r --force-all apt-utils // works!
```

```
sudo apt-get install -f
	-or-
sudo apt-get -f remove
```

```
sudo apt-get install aptitude
sudo aptitude install <package-name>
```

```
sudo apt-get update
sudo apt-get clean
sudo apt-get autoremove
```

```
sudo apt --fix-broken install
sudo apt-get update && sudo apt-get upgrade
sudo dpkg --configure -a
sudo apt-get install -f
sudo apt-get install <package-name>
```

Enable zram and zswap // Not really needed
	https://www.youtube.com/watch?v=RGVt16xiERc

Zswap
```
cat /sys/module/zswap/parameters/enabled //returns boolean
sudo -i
echo 1 > /sys/module/zswap/parameters/enabled
cat /sys/module/zswap/parameters/enabled
nano /etc/default/grub
"grub_cmdline_linux_default= (add) zswap.enabled=1"
```
https://www.youtube.com/watch?v=XAJoszyzXPs
https://www.addictivetips.com/ubuntu-linux-tips/enable-zswap-on-linux/
https://ubuntu-mate.community/t/enable-zswap-to-increase-performance/11302

Enable TMPFS // writes tmp directly to RAM memory instead of disk

```
sudo cp -v /usr/share/systemd/tmp.mount /etc/systemd/system/ 
sudo systemctl enable tmp.mount
<restart machine>
sudo systemctl status tmp.mount
```

psd - Sets browser profiles to run on ram instead

Profile Sync Daemon, also needs to Enable OverlayFS.
https://github.com/graysky2/profile-sync-daemon

```
sudo apt-get install profile-sync-daemon
sudo systemctl enable profile-sync-daemon
sudo systemctl start profile-sync-daemon
```

PulseAudio
```
alsamixer // front mic boost
sudo alsactl store // stores alsamixer configuration? \
```
youtube-dl

```
-x --audio-format mp3
youtube-dl -f 22 -c -i --external-downloader-args '-n 10' --external-downloader /usr/bin/axel https://www.youtube.com/watch?v=yNj5vFEsp74
youtube-dl -x --audio-format mp3 --external-downloader-args '-n 10' --external-downloader /usr/bin/axel https://www.youtube.com/watch?v=_iQ7f9vV7cs
-f bestvideo+bestaudio --merge-output-format mkv
youtube-dl --external-downloader-args '-n 10' --external-downloader /usr/bin/axel https://www.youtube.com/watch?v=abjRQeYmHCo
```

Download reddit hot files: // requires jq and curl
```
curl -sS -H "User-Agent: TikTok Bot 1.0" https://www.reddit.com/r/REDDIT/hot.json?limit=10 | jq -r '.data.children[].data.url_overridden_by_dest' | youtube-dl -qiw --no-warnings -a -
ffplay <mp3_file>
```

Terminal Autocomplete // not needed anymore, this is by default on POP
	https://unix.stackexchange.com/questions/55203/bash-autocomplete-first-list-files-then-cycle-through-them/55632#55632
```
bind "TAB:complete"; bind "set show-all-if-ambiguous on"
bind "TAB:complete"; bind "set show-all-if-ambiguous off"

// make tab cycle through commands after listing
bind '"\t":menu-complete'
bind "set show-all-if-ambiguous on"
bind "set completion-ignore-case on"
bind "set menu-complete-display-prefix on"
```

Microphone Noise Fix // Not Needed anymore
	https://www.youtube.com/watch?v=lTodCeVAfpI
```
sudo nano /etc/pulse/default.pa
load-module module-echo-cancel source_name=noechosource sink_name=noechosink
// ctrl+O to save
// ctrl+X to exit
pulseaudio -k
```

One Time Echo Microphone Fix 
```
pactl load-module module-echo-cancel source_name=noechosource sink_name=noechosink
// and once your recording is done, you can turn it back off, with
pactl unload-module module-echo-cancel
```

Latest working is just using "PulseEffects" and setting the NoiseFilter, Gate and Compressor on Input(Mic) // but lowers recording quality

WEBP

```
sudo apt install webp -y
dwebp file.webp -o file.png
find ./ -name "*.webp" -exec dwebp {} -o {}.png \;

ffmpeg -i file.webp out.png ## converts webp to png
	
for x in ls *.webp; do  ffmpeg -i $x ${x%.webp}.jpg;
// converts ANY webp to jpg
```

Services Enabled on Stacer:

		accounts-daemon
		acpid
		apparmor
		avahi-daemon
		blk-availability
		bluetooth (gotta disable it)
		brltty (gotta disable it)
		com.system76.Scheduler
		console-setup
		cron
		cups-browsed (gotta disable it) // Printer on network
		cups // Local Printer
		dmesg
		e2scrub_reap
		finalrd
		irqbalance
		keyboard-setup
		libvirt-guests
		libbirtd
		lm-sensors
		lvm2-monitor
		ModemManager
		network-dispatcher
		networking
		NetworkManager-dispatcher
		NetworkManager-wait-online
		NetworkManager
		nvidia-persistenced
		openvpn //disable it
		pop-upgrade //disable it
		qemu-kvm
		resolvconf
		rsync
		rsynclog
		rtkit-daemon
		secureboot-db
		setvtrgb
		speech-dispatcherd // disable it
		switcheroo-control
		systemd-networkd
		systemd-pstore
		systemd-resolved
		systemd-sysext
		touchegg //disable it
		ua-reboot-cmds
		udisks2
		ufw
		upower
		wpa_supplicant

Disable services that cannot be disabled by stacer: //must be done with systemctl

		touchegg
		bluetooth
		brltty
		pop-upgrade
		openvpn

Disable Touchegg:
```
/etc/xdg/autostart/touchegg.desktop
<rename to "touchegg.desktop.hold">
ex: mv touchegg.desktop touchegg.desktop.hold
```

Disable Bluetooth: 
```
nano /etc/bluetooth/main.conf 
set AutoEnable=false
systemctl disable bluetooth
```

Disable Braille Device Support
```
sytemctl disable brltty // make sure you don't use it
```

Disable OpenVPN
```
systemctl disable openvpn
```

Remove Evolution ? // this removes default mail client
```
sudo apt-get remove evolution
sudo apt-get autoclean
```

ADDENDUM

* nautilus-typeahead
	Allows to navigate the files by typing the filenames instead of jumping to search
	This is far quicker cause in time you get in the mechanical habit
	https://launchpad.net/~lubomir-brindza/+archive/ubuntu/nautilus-typeahead

* libjpeg -turbo
	Very useful for dealing with large datasets of images, for instance when working with photogrametry. Loads images and their thumbnails very quick.
	I wouldn't recomend unless working with 400 images it's not worth the trouble.
	https://www.libjpeg-turbo.org/
