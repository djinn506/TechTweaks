# Nifty Commands

A few nifty linux commands, mostly compiled from <https://linuxcommandlibrary.com/basic/oneliners/>
Why? This is my collection (a subset), mostly geared towards SysAdmin and what I consider useful, has syntax highlighting and it's easy to copypaste into a terminal.

Use caution if you run these commands and make sure you're using them in the right folder.

Log your internet download speed

```bash
echo $(date +%s) > start-time; URL=http://www.google.com;  while true; do  echo $(curl -L --w %{speed_download} -o/dev/null -s $URL) >> bps;  sleep 10; done &
```

Smart renaming

```bash
mmv 'banana_*_*.asc' 'banana_#2_#1.asc'
```

Monitor a file's size

```bash
watch -n60  du /var/log/messages
```

Remove everything except that file

```bash
find . ! -name <FILENAME> -delete
```

Using mplayer to play the audio only but suppress the video

```bash
mplayer -novideo something.mpg
```

Setting global redirection of STDERR to STDOUT in a script

```bash
exec 2>&1
```

Check disk for bad sectors

```bash
badblocks -n -s -b  2048 /dev/sdX
```

Use wget to download one page and all it's requisites for offline viewing

```bash
wget -e robots=off -E -H -k -K -p http://<page>
```

Load file into RAM (cache) for faster accessing for repeated usage

```bash
cat <file> > /dev/null
```

Find biggest 10 files in current and subdirectories and sort by file size

```bash
find . -type f -print0 |  xargs -0  du -h |  sort -hr |  head -10
```

All what exists in dir B and not in dir A will be copied from dir B to new or existing dir C

```bash
rsync -v -r --size-only --compare-dest=../A/ B/ C/
```

Show running services (using systemctl)

```bash
command  systemctl --no-page --no-legend --plain -t  service --state=running
```

renames multiple files that match the pattern

```bash
rename 's/foo/bar/g' *
```

To get you started in vim!

```bash
vimtutor
```

Rotate a set of photos matching their EXIF data.

```bash
jhead -autorot *.jpg
```

Clean swap area after using a memory hogging application

```bash
swapoff -a ;  swapon -a
```

Get the total length of time in hours:minutes:seconds (HH:MM:SS) of all video (or audio) in the current dir (and below)

```bash
find -type f -name "*.avi" -print0 |  xargs -0   mplayer -vo dummy -ao dummy -identify 2>/dev/null |  perl -nle '/ID_LENGTH=([0-9\.]+)/ && ($t +=$1) &&  printf "%02d:%02d:%02d
",$t/3600,$t/60%60,$t%60' |  tail -n 1
```

Use colordiff in side-by-side mode, and with automatic column widths.

```bash
colordiff -yW"`tput cols`" /path/to/file1 /path/to/file2
```

Recursively search for large files. Show size and location.

```bash
find . -size +100000k -exec  du -h {} \;
```

Rename all files which contain the sub-string 'foo', replacing it with 'bar'

```bash
for i in ./*foo*;do  mv -- "$i" "${i//foo/bar}";done
```

Compare directories via diff

```bash
diff -rq dirA dirB
```

Copy with progress bar - rsync

```bash
rsync -rv <src> <dst> --progress
```

Sort the contents of a text file in place.

```bash
sort -g -o list.txt{,}
```

Show bandwidth use oneliner

```bash
while true; do  cat /proc/net/dev;  sleep 1; done |  awk -v dc="date \"+%T\"" '/eth0/{i = $2 - oi; o = $10 - oo; oi = $2; oo = $10; dc | getline d; close(dc);  if (a++)  printf "%s %8.2f KiB/s in %8.2f KiB/s out", d, i/1024, o/1024}'
```

Find all files larger than 500M and less than 1GB

```bash
find / -type f -size +500M -size -1G
```

Quickly get summary of sizes for files and folders

```bash
du -sh *
```

Host cpu performance

```bash
openssl speed md5
```

Find recursively, from current directory down, files and directories whose names contain single or multiple whitespaces and replace each such occurrence with a single underscore.

```bash
find  ./  -name '*'  -exec   rename  's/\s+/_/g'  {}  \;
```

Shows size of dirs and files, hidden or not, sorted.

```bash
du -cs * .[^\.]* |  sort -n
```

Create a bunch of dummy files for testing

```bash
touch {1..10}.txt
```

Find the process that is using a certain port e.g. port 3000

```bash
lsof -P |  grep ':3000'
```

Convert camelCase to underscores (camel_case)

```bash
sed -r 's/([a-z]+)([A-Z][a-z]+)/\1_\l\2/g' file.txt
```

Search for a single file and go to it

```bash
cd $(dirname $(find ~ -name emails.txt))
```

Plays Music from SomaFM

```bash
read -p "Which station? ";  mplayer --reallyquiet -vo none -ao sdl http://somafm.com/startstream=${REPLY}.pls
```

Cleanup firefox's database.

```bash
pgrep -u `id -u` firefox-bin ||  find ~/.mozilla/firefox -name '*.sqlite'|(while  read -e f; do  echo 'vacuum;'|sqlite3 "$f" ; done)
```

Parallel file downloading with wget

```bash
wget -nv http://en.wikipedia.org/wiki/Linux -O- |  egrep -o "http://[^[:space:]]*.jpg" |  xargs -P 10 -r -n 1  wget -nv
```

Record microphone input and output to date stamped mp3 file

```bash
arecord -q -f  cd -r 44100 -c2 -t  raw | lame -S -x -h -b 128 - `date +%Y%m%d%H%M`.mp3
```

Empty a file

```bash
:>  file
```

Kill all Zombie processes (Guaranteed!)

```bash
kill -9 `ps -xaw -o state -o ppid |  grep Z |  grep -v PID |  awk '{print $2}'`
```

List all file extensions in a directory

```bash
find . -type f |  awk -F'.' '{print $NF}' |  sort|  uniq -c |  sort -g

```

ssh to machine behind shared NAT

```bash
ssh -NR 0.0.0.0:2222:127.0.0.1:22 user@jump.host.com
```

all out!!

```bash
pkill -KILL -u username
```

Run a ext4 file system check and badblocks scan with progress info

```bash
fsck.ext4 -cDfty -C 0 /dev/sdxx
```

Generate a random password 30 characters long

```bash
gpg --gen-random --armor 1 30
```

Execute a command, convert output to .png file, upload file to imgur.com, then returning the address of the .png.

```bash
imgur(){ $*|convert label:@- png:-|curl -F "image=@-" -F "key=1913b4ac473c692372d108209958fd15" http://api.imgur.com/2/upload.xml| grep -Eo "<original>(.)*</original>" |  grep -Eo "http://i.imgur.com/[^<]*";}
```

Find Malware in the current and sub directories by MD5 hashes

```bash
IFS=$'
' &&  for f in `find . -type f -exec  md5sum "{}" \;`; do  echo $f |  sed -r 's/^[^ ]+/Checking:/';  echo $f |  cut -f1 -d' ' |  netcat hash.cymru.com 43 ; done
```

Update all packages installed via homebrew

```bash
brew update &&  brew upgrade ` brew outdated`
```

Recursively find top 20 largest files (> 1MB) sort human readable format

```bash
find . -type f -print0 |  xargs -0  du -h |  sort -hr |  head -20
```

Block an IP address from connecting to a server

```bash
iptables -A INPUT -s 222.35.138.25/32 -j DROP
```

pattern match in awk - no grep

```bash
awk '/pattern1/ && /pattern2/ && !/pattern3/ {print}'
```

Files extension change

```bash
rename .oldextension .newextension *.oldextension
```

List and delete files older than one year

```bash
find <directory path> -mtime +365 -and -not -type d -delete
for all flv files in a dir, grab the first frame and make a jpg.
$ for f in *.flv; do  ffmpeg -y -i "$f" -f image2 -ss 10 -vframes 1 -an "${f%.flv}.jpg"; done
```

Find running binary executables that were not installed using dpkg

```bash
cat /var/lib/dpkg/info/*.list > /tmp/listin ;  ls /proc/*/exe |xargs -l  readlink |  grep -xvFf /tmp/listin;  rm /tmp/listin
```

Add prefix onto filenames

```bash
rename 's/^/prefix/' *
```

Kill processes that have been running for more than a week

```bash
find /proc -user myuser -maxdepth 1 -type d -mtime +7 -exec  basename {} \; |  xargs  kill -9
```

Copy a folder tree through ssh using compression (no temporary files)

```bash
ssh <host> ' tar -cz /<folder>/<subfolder>' |  tar -xvz
```

Insert the last argument of the previous command

```bash
<ALT> .
```

Countdown Clock

```bash
MIN=1 &&  for i in $(seq $(($MIN*60)) -1 1); do  echo -n "$i, ";  sleep 1; done;  echo -e "BOOOM! Time to start."
```

Continue a current job in the background

```bash
<ctrl+z>  bg
```

Ask user to confirm

```bash
Confirm() {  read -sn 1 -p "$1 [Y/N]? "; [[ $REPLY = [Yy] ]]; }
```

Run a command when a file is changed

```bash
while  inotifywait -e modify /tmp/myfile; do firefox; done
```

Watch several log files of different machines in a single multitail window on your own machine

```bash
multitail -l 'ssh machine1 "tail -f /var/log/apache2/error.log"' -l 'ssh machine2 "tail -f /var/log/apache2/error.log"'
```

The fastest remote directory rsync over ssh archival I can muster (40MB/s over 1gb NICs)

```bash
rsync -aHAXxv --numeric-ids --delete --progress -e "ssh -T -c arcfour -o Compression=no -x" user@<source>:<source_dir> <dest_dir>
```

True Random Dice Roll

```bash
tr -cd '1-6' < /dev/urandom |  head -c 1;  echo
```

Using a single sudo to run multiple && arguments

```bash
sudo -s <<< ' apt update -y &&  apt upgrade -y'
```

Find broken symlinks

```bash
find . -type l ! -exec  test -e {} \; -print
```

Delete the specified line

```bash
sed -i 8d ~/.ssh/known_hosts
```

Append stdout and stderr to a file, and print stderr to the screen [bash]

```bash
somecommand 2>&1 >> logfile |  tee -a logfile
```

List programs with open ports and connections

```bash
lsof -i
```

Find corrupted jpeg image files

```bash
find . -name "*jpg" -exec jpeginfo -c {} \; |  grep -E "WARNING|ERROR"
```

How fast is the connexion to a URL, some stats from curl

```bash
URL="http://www.google.com";curl -L --w "$URL
DNS %{time_namelookup}s  conn %{time_connect}s   time %{time_total}s
Speed %{speed_download}bps Size %{size_download}bytes
" -o/dev/null -s $URL
```

List Network Tools in Linux

```bash
apropos network |more
```

Sort all running processes by their memory & CPU usage

```bash
ps aux --sort=%mem,%cpu
```

Grep by paragraph instead of by line.

```bash
grepp() { [ $# -eq 1 ] &&  perl -00ne "print  if /$1/i" ||  perl -00ne "print  if /$1/i" < "$2";}
```

Remote backups with tar over ssh

```bash
tar jcpf - [sourceDirs] |ssh user@host "cat > /path/to/backup/backupfile. tar.bz2"
```

List bash functions defined in .bash_profile or .bashrc

```bash
compgen -A function
```

Find all active ip's in a subnet

```bash
sudo  arp-scan -I eth0 192.168.1.0/24
```

Analyze traffic remotely over ssh w/ wireshark

```bash
ssh root@HOST  tcpdump -U -s0 -w - 'not port 22' |  wireshark -k -i -
```

Resize an image to at least a specific resolution

```bash
convert -resize '1024x600^' image.jpg small-image.jpg
```

Tail a log over ssh

```bash
ssh  -t remotebox "tail -f /var/log/remote.log"
```

Replace spaces in filenames with underscores

```bash
rename -v 's/ /_/g' *
```

Play music from youtube without download

```bash
wget -q -O - `youtube-dl -b -g $url`|  ffmpeg -i - -f mp3 -vn -acodec libmp3lame -|  mpg123  -
```

Update twitter via curl (and also set the "from" bit)

```bash
curl -u twitter-username -d status="Hello World, Twitter!" -d source="cURL" http://twitter.com/statuses/update.xml
```

Make ISO image of a folder

```bash
mkisofs -J -allow-lowercase -R -V "OpenCD8806" -iso-level 4 -o OpenCD.iso ~/OpenCD
```

Timer with sound alarm

```bash
sleep 3s &&  espeak "wake up, you bastard" 2>/dev/null
```

Monitor Memory Usage

```bash
watch  vmstat -sSM
```

Use last argument of last command

```bash
file !$
```

Extract audio from a video

```bash
ffmpeg -i video.avi -f mp3 audio.mp3
```

Add calendar to desktop wallpaper

```bash
convert -font -misc-fixed-*-*-*-*-*-*-*-*-*-*-*-* -fill  black -draw "text 270,260 \" `cal` \"" testpic.jpg newtestpic.jpg
```

Send keypresses to an X application

```bash
xvkbd -xsendevent -text "Hello world"
```

restoring some data from a corrupted text file

```bash
(  cat badfile.log ;  tac badfile.log |  tac ) > goodfile.log
```

Move all images in a directory into a directory hierarchy based on year, month and day based on exif information

```bash
exiftool '-Directory<DateTimeOriginal' -d %Y/%m/%d  dir
```

Show directories in the PATH, one per line

```bash
echo $PATH |  tr \: \
```

Search back through previous commands

```bash
Ctrl-R <search-text>
```

Generate Random Password

```bash
pwgen -Bs 10 1
```

Copy with progress

```bash
rsync --progress file1 file2
```

Convert a Nero Image File to ISO

```bash
dd bs=1k if=image.nrg of=image.iso skip=300
```

Make, or run a script, everytime a file in a directory is modified

```bash
while true; do  inotifywait -r -e MODIFY dir/ && make; done;
```

Reuse last parameter

```bash
!$
```

Protect directory from an overzealous rm -rf *

```bash
cd <directory>;  touch ./-i
```

Delete command line last word

```bash
ctrl+w
```

Use top to monitor only all processes with the same name fragment 'foo'

```bash
top -p $(pgrep -d , foo)
```

Run any GUI program remotely

```bash
ssh -fX <user>@<host> <program>
```

Use all the cores or CPUs when compiling

```bash
make -j 4
```

replace 4 with number of cores

grep -v with multiple patterns.

```bash
grep 'test' somefile |  grep -vE '(error|critical|warning)'
```

Generate a Random MAC address

```bash
openssl rand -hex 6 |  sed 's/\(..\)/\1:/g; s/.$//'
```

Rename all .jpeg and .JPG files to have .jpg extension

```bash
rename 's/\.jpe?g$/.jpg/i' *
```

Download a file and uncompress it while it downloads

```bash
wget http://URL/FILE. tar.gz -O - |  tar xfz -
```

Short and elegant way to backup a single file before you change it.

```bash
cp httpd.conf{,.bk}
```

Find the most recently changed files (recursively)

```bash
find . -type f -printf '%TY-%Tm-%Td %TT %p
' |  sort
```

Get all pdf and zips from a website using wget

```bash
wget --reject html,htm --accept pdf,zip -rl1 url
```

Remove executable bit from all files in the current directory recursively, excluding other directories

```bash
chmod -R -x+X *
```

Download and unpack tarball without leaving it sitting on your hard drive

```bash
wget -qO - http://example.com/path/to/blah. tar.gz |  tar xzf -
```

Disable history for current shell session

```bash
unset HISTFILE
```

Save an HTML page, and covert it to a .pdf file

```bash
wget $URL | htmldoc --webpage -f "$URL".pdf - ;  xpdf "$URL".pdf &
```

Cleanup firefox's database.

```bash
find ~/.mozilla/firefox/ -type f -name "*.sqlite" -exec  sqlite3 {} VACUUM \;
```

Purge configuration files of removed packages on debian based systems

```bash
aptitude purge '~c'
sudo apt-get purge '~c'
```

Purge configuration files of removed packages on debian based systems

```bash
aptitude purge '~c'
```

Extract audio from Flash video (*.flv) as mp3 file

```bash
ffmpeg -i video.flv -vn -ar 44100 -ac 2 -ab 192k -f mp3 audio.mp3
```

Recursively compare two directories and output their differences on a readable format

```bash
diff -urp /originaldirectory /modifieddirectory
```

Limit the cpu usage of a process

```bash
sudo  cpulimit -p pid -l 50
```

Convert filenames in current directory to lowercase

```bash
rename 'y/A-Z/a-z/' *
```

(Regular Expression)

Find Duplicate Files (based on size first, then MD5 hash)

```bash
fdupes -r .
```

Create strong, but easy to remember password

```bash
read -s pass;  echo $pass |  md5sum |  base64 |  cut -c -16
```

View the newest xkcd comic. // needs feh and magic bytes

```bash
xkcd(){  wget -qO- http://xkcd.com/|tee >(feh $(grep -Po '(?<=")http://imgs[^/]+/comics/[^"]+\.\w{3}'))|grep -Po '(?<=(\w{3})" title=").*(?=" alt)';}
```

Nicely display permissions in octal format with filename

```bash
stat -c '%A %a %n' *
```

Run a long job and notify me when it's finished

```bash
./my-really-long-job.sh &&  notify-send "Job finished"
```

List installed deb packages by size

```bash
dpkg-query -Wf '${Installed-Size}\t${Package}
' |  sort -n
```

Copy working directory and compress it on-the-fly while showing progress

```bash
tar -cf - . |  pv -s $(du -sb . |  awk '{print $1}') |  gzip > out.tgz
```

Bind a key with a command

```bash
bind -x '"\C-l":ls -l'
```

Notify yourself when a long-running command which has ALREADY STARTED is finished

```bash
<ctrl+z> fg; notify_me
```

Find all file larger than 500M

```bash
find / -type f -size +500M
```

Easily find megabyte eating files or directories

```bash
alias dush="du -sm *|sort -n|tail"
```

Exclude .svn, .git and other VCS junk for a pristine tarball

```bash
tar --exclude-vcs -cf src. tar src/
```

Block known dirty hosts from reaching your machine

```bash
wget -qO - http://infiltrated.net/blacklisted|awk '!/#|[a-z]/&&/./{print "iptables -A INPUT -s "$1" -j DROP"}'
```

Search recursively to find a word or phrase in certain file types, such as C code

```bash

find . -name "*.[ch]" -exec  grep -i -H "search pharse" {} \;
```

Intercept, monitor and manipulate a TCP connection.

```bash
mkfifo /tmp/fifo;  cat /tmp/fifo |  nc -l -p 1234 |  tee -a to.log |  nc machine port |  tee -a from.log > /tmp/fifo
```

Find files that have been modified on your system in the past 60 minutes

```bash
sudo  find / -mmin 60 -type f
```

Show apps that use internet connection at the moment.

```bash
lsof -P -i -n |  cut -f 1 -d " "|  uniq |  tail -n +2
```

Remind yourself to leave in 15 minutes

```bash
leave +15
```

Compare two directory trees.

```bash
diff <(cd dir1 &&  find | sort) <(cd dir2 &&  find | sort)
```

List only the directories

```bash
ls -d */
```

Download all images from a site

```bash
wget -r -l1 --no-parent -nH -nd -P/tmp -A".gif,.jpg" http://example.com/images
```

Create a quick back-up copy of a file

```bash
cp file.txt{,.bak}
```

Start COMMAND, and kill it if still running after 5 seconds

```bash
timeout 5s COMMAND
```

Send pop-up notifications on Gnome

```bash
notify-send ["<title>"] "<body>"
```

Weather

```bash
curl wttr.in/kamchatka
```

Recursively remove all empty directories

```bash
find . -type d -empty -delete
```

Monitor progress of a command

```bash
pv access.log |  gzip > access.log.gz
```

Search for a pattern string inside all files in the current directory

```bash
grep -RnisI <pattern> *
```

Get the 10 biggest files/folders for the current directory

```bash
du -s * |  sort -n |  tail
```

Record a screencast and convert it to an mpeg

```bash
ffmpeg -f x11grab -r 25 -s 800x600 -i :0.0 /tmp/outputFile.mpg
```

Generate Password

```bash
strings /dev/urandom |  grep -o '[[:alnum:]]' |  head -n 30 |  tr -d '
';  echo
```

Stream youtube audio

```bash
i="8uyxVmdaJ-w";mplayer -fs $(curl -s "http://www.youtube.com/get_video_info?&video_id=$i" |  echo -e $(sed 's/%/\\x/g;s/.*\(v[0-9]\.lscache.*\)/http:\/\/\1/g') |  grep -oP '^[^|,]*')
```
