SYSTEM INFORMATION COMMANDS
===========================

(#) Show only errors and warnings: `dmesg --level=err,warn`
(#) View dmesg output in human readable format: `dmesg -T`
(#) Get an audio notification if a new device is attached to your computer: `dmesg -tW -l notice | gawk '{ if ($4 == "Attached") { system("echo New device attached | espeak") } }`
(#) Dmesg: follow/wait for new kernel messages: `dmesg -w`
(#) The proper way to read kernel messages in realtime.: `dmesg -wx`

($) Query graphics card: `lspci -nnk | grep -i VGA -A2`
($) Query sound card: `lspci -nnk | grep -i audio -A2`
($) Quick and dirty hardware summary: `(printf "\nCPU\n\n"; lscpu; printf "\nMEMORY\n\n"; free -h; printf "\nDISKS\n\n"; lsblk; printf "\nPCI\n\n"; lspci; printf "\nUSB\n\n"; lsusb; printf "\nNETWORK\n\n"; ifconfig) | less`
($) Percental CPU scaled load average: `printf "System load (1m/5m/15m): "; for l in 1 2 3 ; do printf "%.1f%s" "$(( $(cat /proc/loadavg | cut -f $l -d " ") * 100 / $(nproc) ))" "% "; done; printf "\n"`
($) Check whether laptop is running on battery or cable: `eval "$(printf "echo %s \$((%i * 100 / %i))\n" $(cat $(find /sys -name energy_now 2>/dev/null | head -1 | xargs dirname)/{status,energy_{now,full}}))%"`
($) Get battery stats: `DIR=$(find /sys -name energy_now -exec dirname {} \\; 2>/dev/null | head -1) && for STAT in $(find $DIR -maxdepth 1 -type f); do printf "%s %s\n" "$(basename $STAT)" "$(cat $STAT)"; done`
($) Is your machine plugged in?: `cat /sys/class/power_supply/AC/online`
($) Print your cpu intel architecture family: `cat /sys/devices/cpu/caps/pmu_name`
($) (OSX) Get CPU thermal data: `sysctl machdep.xcpm.cpu_thermal_level`
($) Quick integer CPU benchmark: `time cat /proc/cpuinfo | grep proc | wc -l| xargs seq | parallel -N 0 echo "2^2^20" '|' bc`
($) Small CPU benchmark with PI, bc and time.: `time cat /proc/cpuinfo | grep proc | wc -l | xargs seq | parallel -N 0 echo "scale=4000\; a\(1\)\*4" '|' bc -l`
($) Get device info for all USB devices: `echo /sys/bus/usb/devices/* | xargs udevadm info -q property -p`

($) List of commands you use most often: `history | awk '{a[$2]++}END{for(i in a){print a[i] " " i}}' | sort -rn | head`
($) List of commands you use most often: `history | awk '{print $2}' | sort | uniq -c | sort -rn | head`
($) List all of the commands on your system that have a man page and a short description: `whatis -r .`

($) Create a file and manipulate the date: `touch -d '-1 year' /tmp/oldfile`
($) Get full path of a file: `readlink -f FILE`

($) See if a file is being used: `lsof /path/to/file`
($) To find out fonts that the `brave` browser is using: `for proc in $(ps -o pid --no-headers -C brave); do (lsof -p $proc | grep fonts | cut -f4); done`
($) List current processes writing to hard drive: `lsof | grep -e "[[:digit:]]\+w"`
($) Find which ports you probably want to open in your firewall on a fresh installed machine: `lsof -i -nlP | awk '{print $9, $8, $1}' | sed 's/.*://' | sort -u`

PROCESSES, DAEMONS AND ADMINISTRATION
=====================================
($) List all running processes: `ps aux`
($) List all running processes including the full command string: `ps auxww`
($) Search for a process that matches a string: `ps aux | grep string`
($) List all processes of the current user in extra full format: `ps --user $(id -u) -F`
($) List all processes of the current user as a tree: `ps --user $(id -u) f`
($) Get the parent PID of a process: `ps -o ppid= -p pid`
($) Sort processes by memory consumption: `ps --sort size`
($) List currently running processes: `ps auxww`
($) List all process of current user (full info): `ps --user NAME -F`
($) Show most memory intensive process: `ps axch -o cmd:15,%mem --sort=-%mem`
($) Show most CPU intensive process: `ps axch -o cmd:15,%cpu --sort=-%cpuw`
($) Monitor my process group tree: `watch "ps --forest -o pid=PID,tty=TTY,stat=STAT,time=TIME,pcpu=CPU,cmd=CMD -g $(ps -o sid= -p $(pgrep -f "<my_process_name>"))"`
($) Show top 50 running processes ordered by highest memory/cpu usage refreshing every 1s: `watch -n1 "ps aux --sort=-%mem,-%cpu | head -n 50"`
($) Perform Real-time Process Monitoring Using Watch Utility: `watch -n 1 'ps -eo pid,ppid,cmd,%mem,%cpu --sort=-%mem | head'`
($) Sort processes by CPU Usage: `ps auxk -%cpu | head -n10`
($) Top 10 Memory Processes (reduced output to applications and %usage only): `ps aux | sort -rk 4,4 | head -n 10 | awk '{print $4,$11}'`
($) Top 10 Memory Processes: `ps aux | sort -rk 4,4 | head -n 10`

($) List all of the signals kill can send: `kill -l`
($) Hang up process: `kill -1 process_id`
($) Send interrupt to process: `kill -2 process_id`
($) Immediately terminate a process: `kill -9 process_id`
($) Hang up all processes that match a name: `pkill -9 "process_name"`
($) Close virtual terminal or logout, keeping all subprocess running: `disown -a && exit`

($) Show systemctl failed units: `systemctl --failed`
($) Show the status of a unit: `systemctl status NAMEOFUNIT`
($) Show all installed services: `systemctl list-unit-files --state=enabled --no-pager`
(#) Programmatic way to find and set your timezone: `timedatectl set-timezone $(curl -s worldtimeapi.org/api/ip.txt | sed -n 's/^timezone: //p')`
(#) Add a DNS server on the fly: `systemd-resolve --interface <NombreInterfaz> --set-dns <IPDNS> --set-domain mydomain.com`
(#) Restart Bluetooth: `systemctl restart bluetooth`
(#) Command to logout all the users in one command: `who | awk '!/root/{ cmd="/sbin/pkill -KILL -u " $1; system(cmd)}'`

($) List all accessed configuration files while executing a program in linux terminal (improved version): `strace 2>&1 <any_executable> | egrep -o "\".*\.conf\""`
($) Find which config-file is read: `strace 2>&1  geany | grep geany.conf`
($) Linux system calls of MySQL process: `strace -c -p $(pidof -s mysqld) -f -e trace=all`

(#) Using a single sudo to run multiple && arguments: `sudo -s <<< 'apt update -y && apt upgrade -y'`
(#) Using a single sudo to run multiple && arguments: `sudo sh -c 'apt update -y && apt upgrade -y'`

(#) Change a user's password: `passwd username`
(#) Change a user's name: `usermod -l newname user`
(#) Add user to supplementary groups (mind the whitespace): `usermod -a -G group1,group2 user`
(#) Create a new home directory for a user and move their files to it: `usermod -m -d path/to/home user`

SYSTEMD
=======

($) List the status of services (systemd): `systemctl status`
($) List failed services (systemd): `systemctl --failed`
($) Start a service system-wide (systemd): `systemctl start SERVICE`
($) Start an user service (systemd): `systemctl --user start SERVICE`
($) Enable a service system-wide (systemd): `systemctl enable SERVICE`
($) Enable a service system-wide now (systemd): `systemctl enable --now SERVICE`

OPENRC
======

(#) Start the ssh daemon in the default runlevel at boot: `rc-update add sshd default`
(#) Start the sshd service now: `rc-service sshd start`
(#) Check if the sshd service is running: `rc-service sshd status`
(#) Restart the sshd service: `rc-service sshd restart`
(#) Stop the sshd service: `rc-service sshd stop`
(#) List services, their status, and the runlevels they belong to: `rc-status --all`
(#) Show enabled services and the runlevels they belong to (not whether they are running or not, just if they are enabled): `rc-update show`

FILESYSTEMS
===========
($) Create ext4 filesystem with big count of inodes: `mkfs.ext4 -T news /dev/sdcXX`
($) Show allocated disk space:: `df -klP -t xfs -t ext2 -t ext3 -t ext4 -t reiserfs | grep -oE ' [0-9]{1,}( +[0-9]{1,})+' | awk '{sum_used += $1} END {printf "%.0f GB\n", sum_used/1024/1024}'`
($) Show used disk space:: `df -klP -t xfs -t ext2 -t ext3 -t ext4 -t reiserfs | grep -oE ' [0-9]{1,}( +[0-9]{1,})+' | awk '{sum_used += $2} END {printf "%.0f GB\n", sum_used/1024/1024}'`

(#) Flash an image onto a USB drive using cat: `cat path/to/archlinux-version-x86_64.iso > /dev/sdx`
(#) Flash an image onto a USB drive using cp: `cp path/to/archlinux-version-x86_64.iso /dev/sdx`
(#) Flash an image onto a USB drive using dd: `dd bs=4M if=path/to/archlinux-version-x86_64.iso of=/dev/sdx status=progress oflag=sync`
(#) Flash an image onto a USB drive using tee: `tee < path/to/archlinux-version-x86_64.iso > /dev/sdx`
(#) Write a bootable Linux .iso file directly to a USB-stick: `wget -O /dev/sdb https://cdimage.ubuntu.com/daily-live/current/eoan-desktop-amd64.iso`
(#) Mount an ISO: `mount -o loop /path/to/image.iso /media/mountpoint`
(#) Rip an ISO: run `isosize -x /dev/sr0` to determine sector count and size, then run `dd if=/dev/sr0 of=discimage.iso bs=SECTOR_SIZE count=SECTOR_COUNT status=progress`

LISTS AND STORAGE
=================
($) List permissions and ownership of contents of directory: `ls -l`
($) List hidden files, too, in a column: `ls -lA`
($) List all subdirectories (but omit . and ..): `ls -A1D`
($) List all contents, reversing results (most recent modified files on bottom): `ls -trA1`

($) Summarize the size of current directory on disk in a human-readable format: `du -sh`
($) See free disk space and mount points in a human readable format: `df -h`
($) Get partitions that are over 50% usage: `df -h | awk '{a=$5;gsub(/%/,"",a);if(a > 50){print $0}}'`
($) Currently mounted filesystems in nice layout: `mount | column -t`
($) Get the top 10 largest files ordered by size descending, starting from the current folder, recursively: `find . -printf '%s %p\n'| sort -nr | head -10`
($) Find 10 largest folders: `du -hsx * | sort -rh | head -10`
($) List the size (in human readable form) of all sub folders from the current location: `du -h -d1`
($) Du command without showing other mounted file systems: `du -h --max-depth=1 --one-file-system /`
($) Get total of inodes of root partition: `du --total --inodes / | egrep 'total$'`

($) Find top 10 largest files in /var directory (subdirectories and hidden files included ): `tree -ihafF /var | tr '[]' ' '| sort -k1hr|head -10`
($) Show permissions and ownership of directories to two levels: `tree -dpug -L 2`
($) Show directory structure to two levels with file size: `tree -ash -L 2`

($) Change to previous directory: `cd -`
($) Create multiple subfolders in one command.: `mkdir -p /path/folder{1..4}`
($) Create multiple subfolders in one command.: `mkdir -p /path/{folder1,folder2,folder3,folder4}`

DD
==
($) Iso to USB with dd and show progress status: `dd if=/backup/archlinux.iso of=/dev/sdb status=progress`
($) Iso to USB with dd and show progress status: `dd if=/home/kozanoglu/Downloads/XenServer-7.2.0-install-cd.iso | pv --eta --size 721420288 --progress --bytes --rate --wait > /dev/sdb`
($) Make a bootable USB drive from an isohybrid file (such like `archlinux-xxx.iso`) and show the progress: `dd if=file.iso of=/dev/usb_drive status=progress`
($) Clone a drive to another drive with 4 MiB block, ignore error and show progress: `dd if=/dev/source_drive of=/dev/dest_drive bs=4M conv=noerror status=progress`
($) Generate a file of 100 random bytes by using kernel random driver: `dd if=/dev/urandom of=random_file bs=100 count=1`
($) Benchmark the write performance of a disk: `dd if=/dev/zero of=file_1GB bs=1024 count=1000000`
($) Generate a system backup into an IMG file and show the progress: `dd if=/dev/drive_device of=path/to/file.img status=progress`
($) Restore a drive from an IMG file and show the progress: `dd if=path/to/file.img of=/dev/drive_device status=progress`
($) Check progress of an ongoing dd operation (Run this command from another shell): `kill -USR1 $(pgrep ^dd)`

GREP
====
($) Search for a pattern within a file: `grep "SeArChPaTtErN" path/to/file`
($) Only show matching patterns (o) and line number (n): `grep -on "SeArChPaTtErN" file.cpp`
($) Search for a pattern with case-insensitivity: `grep -i "TODO" file.cpp`
($) Search for a pattern in all files recursively from current directory (skip binary files): `grep -rnI "SeArChPaTtErN" .`

($) Find a matching pattern and print 3 lines after the match: `grep -A3 "SeArChPaTtErN" path/to/file`
($) Find a matching pattern and print 3 lines before the match: `grep -B3 "SeArChPaTtErN" path/to/file`
($) Find a matching pattern and print 3 lines before and after the match: `grep -C3 "SeArChPaTtErN" path/to/file`
($) Match all line that do not match a pattern: `grep -v "roses" poem.txt`
($) Search for an exact string (disables regular expressions): `grep -F "exact_string" path/to/file`
($) Use Perl regex to find the first occurence of 'K' and every character thereafter: `grep -Po '^(.*?:\K)?.*'`
($) Colorize grep output: `grep --color -E 'pattern|$' file`
($) Colorize sequences of digits: `echo abcd89efghij340.20kl | grep --color -e "[0-9]\+" -e "$"`
($) Extract email addresses from file.txt: `grep -Eio '([[:alnum:]_.-]{1,64}@[[:alnum:]_.-]{1,252}?\.[[:alpha:].]{2,6})' file.txt`

HEADS AND TAILS
===============
($) Get the last 10 lines of a file: `tail file.txt`
($) Get the last 20 lines of a file: `tail -n20 file.txt`

($) Watch the output of a file continously and filter: `tail -f path/to/file | grep "SeArChPaTtErN"`
($) Watch and filter a file continously (even if it doesn't exist yet): `tail -F path/to/file | grep "SeArChPaTtErN"`
($) Filter the output of a file continously using tail and grep: `tail -f file.txt | grep --line-buffered ""`
($) Display the end of a logfile as new lines are added to the end: `tail -f logfile`
($) Re-execute a command using a saved /proc/pid/cmdline file: `tail -zn+2 $CMDLINE_FILENAME | xargs -0 $COMMAND`
($) Realtime lines per second in a log file, with history: `tail -f access.log | pv -l -i10 -r -f 2>&1 >/dev/null  | tr /\\r \ \\n`
($) Tail a log and replace according to a sed pattern: `tail -F logfile|while read l; do sed 's/find/replace/g' <<< $l; done`

($) Output the first 10 lines of a file: `head filename`
($) Output the first 20 lines of a file: `head -n20 filename`

($) Output the first 239 bytes of a file: `head -c239 filename`
($) Output everything but the last 24 lines of a file: `head -n -24 filename`
($) Output everything but the last 172 bytes of a file: `head -c -172 filename`

CUT, TR, AND COLUMN
===================
($) Cut out the first sixteen characters of each line of stdin: `cut -c 1-16`
($) Cut out the first sixteen characters of each line of the given files: `cut -c 1-16 file`
($) Cut out everything from the 3rd character to the end of each line: `cut -c 3-`
($) Cut out the fifth field of each line, using a colon as a field delimiter (default delimiter is tab): `cut -d':' -f5`
($) Cut out the 2nd and 10th fields of each line, using a semicolon as a delimiter: `cut -d';' -f2,10`
($) Cut out the fields 3 through to the end of each line, using a space as a delimiter: `cut -d' ' -f3-`
($) Extract column from csv file: `cut -d"," -f9`

($) Replace all occurrences of a character in a file, and print the result: `tr find_character replace_character < filename`
($) Replace all occurrences of a character from another command's output: `echo text | tr find_character replace_character`
($) Map each character of the first set to the corresponding character of the second set: `tr 'abcd' 'jkmn' < filename`
($) Delete all occurrences of the specified set of characters from the input: `tr -d 'input_characters' < filename`
($) Compress a series of identical characters to a single character: `tr -s 'input_characters' < filename`
($) Translate the contents of a file to upper-case: `tr "[:lower:]" "[:upper:]" < filename`
($) Strip out non-printable characters from a file: `tr -cd "[:print:]" < filename`

($) Format the output of a command for a 30 characters wide display: `printf "header1 header2\nbar foo\n" | column --output-width 30`
($) Split columns automatically and auto-align them in a tabular format: `printf "header1 header2\nbar foo\n" | column --table`
($) Specify the column delimiter character for the `--table` option (e.g. "," for CSV) (defaults to whitespace): `printf "header1,header2\nbar,foo\n" | column --table --separator ,`
($) Fill rows before filling columns: `printf "header1\nbar\nfoobar\n" | column --output-width 30 --fillrows`
($) Nicely format a CSV file: `column -s, -t < somefile.csv | less -#2 -N -S`

SEQUENCES AND LOOPS
===================
($) Shell bash iterate number range with for loop: `seq 10 20`
($) Generate a sequence of numbers.: `seq 12`
($) Generate a sequence of numbers.: `for ((i=1; i<=99; ++i)); do echo $i; done`
($) Shell bash iterate number range with for loop: `for((i=1;i<=10;i++)){ echo $i; }`
($) Iterate over a number range: `for i in $(seq 1 5) ; do echo $i ; done`
($) Iterate over a number range: `for i in {1..10}; do echo $i; done`

FIND
====
($) Find all jpg files in 'work' directory: `find ./work -name "*.jpg"`
($) Find files only, matching a glob pattern: `find -name "*homework*" -type f`
($) Find all executable files: `find -executable -type f`
($) Find all files in current directory excluding `.wine` and `.git` directories: `find . -type f \! \( -path '*/\.wine/*' -o -path '*/\.git/*' \)`
($) Find files only, matching a glob pattern, but omit '.git' directory: `find . -name "homework" -type f \! \( -path '*/\.git/*' \)`
($) Find today's files only: `find directory_path -maxdepth 1 -daystart -mtime -1`
($) Find from here, recently accessed files: `find . -type f -print0 | xargs -0 stat --format '%Y :%y %n' | sort -nr | cut -d: -f2- | head`
($) Find image files by common extension: `find . \( -iname '*.tiff' -or -iname '*.tif'  -or -iname '*.bmp'  -or -iname '*.jpeg' -or -iname '*.jpg'  -or -iname '*.gif'  -or -iname '*.png'  -or -iname '*.heic' -or -iname '*.raw' -or -iname '*.cr2' -or -iname '*.nef' -or -iname '*.orf' -or -iname '*.sr2' \)`

($) Find all files matching glob and create one zip file: `find . -name "*.jpg" -print | zip newZipFile.zip -@`
($) Find all files that are larger than 20MB starting in home directory: `find "$HOME" -type f -size +20000k -exec ls -lh {} \; 2> /dev/null | awk '{ print $NF ": " $5 }' | sort -nrk 2,2`
($) Find and delete all files recursively, by extension: `find . -name "*.jpg" -delete`
($) Find out which directory uses most inodes - list total sum of directoryname existing on filesystem: `find /etc -xdev -printf '%h\n' | sort | uniq -c | sort -k 1 -n`
($) Find from here, inside files recursively and replace a string: `find . -type f -exec sed -i 's/OLDSTRING/NEWSTRING/g' {} +`

($) Find all pdfs and add '_small' to the name (renaming and shell substitution): `for FILE in $(find . -name "*.pdf" -print); do mv $FILE ${FILE%%.pdf}_small.pdf; done`
($) Find files, possibly with spaces in filenames and use sxiv to display any images (brute): `find . -type f -print0 | xargs -0 sxiv -t &>/dev/null`
($) Find dupe files by checking md5sum: `find . -type f | grep '([0-9]\{1,9\})\.[^.]\+$' | parallel -n1 -j200% md5sum ::: | awk 'x[$1]++ { print $2 " :::"}'| sed 's/^/Dupe: /g'| sed 's,Dupe,\x1B[31m&\x1B[0m,'`
($) Find ASCII files and extract IP addresses: `find . -type f -exec grep -Iq . {} \; -exec grep -oE "(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)" {} /dev/null \;`
($) Find all images by mime type: `find /path/to/crawl -type f -exec file --mime-type {}  \; | awk '{if ($NF == "image/jpeg") print $0 }'`

($) Check if the same table name exist across different databases: `find . -name "withdrownblocks.frm"  | sort -u | awk -F'/' '{print $3}' | wc  -l`
($) Graphical tree of sub-directories with files: `find . -print | sed -e 's;[^/]*/;|-- ;g;s;-- |;   |;g'`
($) Moving large number of files: `find /source/directory -mindepth 1 -maxdepth 1 -name '*'  -print0 | xargs -0 mv -t /target/directory;`
($) List only empty directories and delete safely (=ask for each): `find . -type d -empty -exec rm -i -R {} \;`
($) Summarize the size of subdirectories in the current directory: `find . -maxdepth 1 -mindepth 1 -type d -exec du -skx {} \; | sort -n`
($) Find ASCII files and extract IP addresses: `find . -type f -exec grep -Iq . {} \; -exec grep -oE "(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)" {} /dev/null \;`
($) Remove scripts tags from *.html and *.htm files under the current directory: `find ./ -type f \( -iname '*.html' -or -iname '*.htm' \) -exec sed -i '/<script/,/<\/script>/d' '{}' \;`
($) Recursive search and replace (with bash only): `find ./ -type f -name "somefile.txt" -exec sed -i -e 's/foo/bar/g' {} \;`
($) Find non-standard files in mysql data directory: `find . -type f -not -name "*.frm" -not -name "*.MYI" -not -name "*.MYD" -not -name "*.TRG" -not -name "*.TRN" -not -name "db.opt"`
($) Find hard linked files (duplicate inodes) recursively: `find . -type f -printf '%10i %p\n' | sort | uniq -w 11 -d -D | less`
($) Find sparse files: `find -type f -printf "%S=:=%p\n" 2>/dev/null | gawk -F'=:=' '{if ($1 < 1.0) print $1,$2}'`
($) Find non-ASCII and UTF-8 files in the current directory: `find . -type f -regex '.*\.\(cpp\|h\)' -exec file {} + | grep "UTF-8\|extended-ASCII"`
($) Replace lines in files with only spaces/tabs with simple empty line (within current directory - recursive): `find . -type f -regex '.*\.\(cpp\|h\)' -exec sed -i 's/^[[:blank:]]\+$//g' {} +`
($) Find the top 10 directories containing the highest number of files: `find / -type f ! -regex '^/\(dev\|proc\|run\|sys\).*' | sed 's@^\(.*\)/[^/]*$@\1@' | sort | uniq -c | sort -n | tail -n 10`
($) Find directory with most inodes/files: `find / -xdev -printf '%h\n' | sort | uniq -c | sort -k 1 -n | tail`
($) Count the total amount of hours of your music collection: `find . -print0 | xargs -0 -P 40 -n 1 sh -c 'ffmpeg -i "$1" 2>&1 | grep "Duration:" | cut -d " " -f 4 | sed "s/.$//" | tr "." ":"' - | awk -F ':' '{ sum1+=$1; sum2+=$2; sum3+=$3; sum4+=$4 } END { printf "%.0f:%.0f:%.0f.%.0f\n", sum1, sum2, sum3, sum4 }'`
($) Find all file extension in current dir.: `find . -type f | perl -ne 'print $1 if m/\.([^.\/]+)$/' | sort -u`

SED
===
($) Replace the first occurrence of a regular expression in each line of a file, and print the result: `sed 's/regex/replace/' filename`
($) Replace all occurrences of an extended regular expression in a file, and print the result: `sed -r 's/regex/replace/g' filename`
($) Replace all occurrences of a string in a file, overwriting the file (i.e. in-place): `sed -i 's/find/replace/g' filename`
($) Replace only on lines matching the line pattern: `sed '/line_pattern/s/find/replace/' filename`
($) Delete lines matching the line pattern: `sed '/line_pattern/d' filename`
($) Print the first 11 lines of a file: `sed 11q filename`
($) Apply multiple find-replace expressions to a file: `sed -e 's/find/replace/' -e 's/find/replace/' filename`
($) Replace separator `/` by any other character not used in the find or replace patterns, e.g., `#`: `sed 's#find#replace#' filename`
($) Replace strings in text: `sed -e 's/dapper/edgy/g' -i /etc/apt/sources.list`
($) This will allow you to browse web sites using "-dump" with elinks while you still are logged in: `sed -i 's/show_formhist = 1/show_formhist = 0/;s/confirm_submit = 0/confirm_submit = 1/g' /etc/elinks/elinks.conf; elinks -dump https://facebook.com`
($) Get line number 12 from a file (n supresses lines not matching): `sed -n '12p' file`
($) Print a specific range of lines (n supresses lines not matching): `sed -n '7,24 p' file`
($) Remove abstracts from a bibtex file: `sed '/^\s*abstract\s*=\s*{[^\n]*},$/ d' input.bib > output.bib`
($) Delete at start of each line until character: `sed 's/^[^:]*://g'`
($) Remove all matches containing a string until its next space: `sed 's/linux-[^ ]* \{0,1\}//g' /path/to/file`
($) Remove ^M characters from file using sed: `sed 's/\r//g' < input.txt >  output.txt`
($) Replace all backward slashes with forward slashes: `echo 'C:\Windows\' | sed 's|\\|\/|g'`
($) Print one line: `sed -n '10p' myfile.txt`
($) Do replacement on all lines except line 5: `sed '5!/s/foo/bar/' file.txt`
($) Do replacement on lines matching regex (eg: lines starting with 'hello'): `sed '/^hello/ s/h/H/' file.txt `
($) Do replacement from line 5 to end of file: `sed '5,$ s/foo/bar/' file.txt `
($) Delete empty files: `sed  '/^$/d' file`
($) Print lines between two regex matches: `sed -nE '/^foo/,/^bar/p' file.txt`
($) Use custom delimiters to make it easy for some strings that contain slashes: `sed 's_/bin/bash_/bin/sh_' file.txt `
($) Custom delimiters for regex address combined with the classical delimiter for substitute command (you could also use there a custom delimiter). Useful for paths.: `sed '\_/bin/bash_s/grep/egrep/' file.txt`
($) Insert a space between lowercase/Uppercase characters using & (which represents the regex match): `sed 's/[a-zA-Z]/& /g' file.txt `
($) Keep the first word of every line (where word is defined by alphanumeric chars + underscores for simplicity sake): `sed -E 's_[a-zA-Z0-9_]+.*_\1_' file.txt `
($) Switch the first two words : `sed -E 's_([a-zA-Z0-9_]*) ([a-zA-Z0-9_]*)_\2 \1_' f1`
($) Remove duplicate words separated by a single space (but not triplicate): `sed -E 's_([a-zA-Z0-9_]+) \1_\1_ig' f1`
($) Search and replace for pattern, write just the lines with the replacements in a new file: `sed  's_foo_bar_w replaced.txt' file.txt  `
($) Multiple replacements: `sed -e 's_foo_bar_' -e 's_hello_HELLO_' file.txt `
($) Multiple commands using the ; operator which in theory concatenates commands: `sed '10p;5i\"INSERTED BEFORE LINE 5" file.txt `
($) Remove comments between lines starting with these two keywords. Empty lines will be put there instead: `sed -E '/start/,/end/ s/#.*//' file.txt `
($) Delete comments starting with # (no empty lines left behind): `sed -E '/^#/d' f1`
($) Insert an empty line after pattern  (after each line containing comment in this case): `sed '/^#/G' file.txt `
($) View lines minus lines between line starting with pattern and end of file : `sed  '/start/,$ d' file.txt `
($) View lines except lines between line starting with pattern and line ending with pattern: `sed -rn '/start/,/end/ !p' file.txt `
($) Print until you encounter pattern then quit: `sed  '/start/q' file.txt `
($) Insert contents of file after a certain line: `sed  '5 r newfile.txt' file.txt `
($) Append text after lines containing regex (AFTER FOO): `sed '/foo/a\AFTER FOO' file.txt `
($) Insert text after lines containing regex (BEFORE FOO): `sed '/foo/i\BEFORE FOO' file.txt `
($) Change line containing regex match: `sed '/foo/c\FOO IS CHANGED' file.txt `
($) Transform text: `sed 'y/abc/ABC/' file.txt `
($) Copy all the comments (starting with #) to a new file: `sed -E '/^#/w comments.txt' file.txt `
($) Print every second line (substitute ~3 for third line, etc): `sed -n '1~2p' file.txt `
($) Edit file in place but also create a backup: `sed -i.bak 's/hello/HELLO/' file.txt `
($) Append two extra lines after regex match: `sed -E '/^#/G G' file.txt `

DIFF AND COMM
=============
($) Comparison between the execution output of the last and penultimate command: `diff <(!!) <(!-2)`
($) Show the difference: `diff file1 file2 --side-by-side --suppress-common-lines`
($) Compare mysql db schema from two different servers: `diff <(mysqldump -hsystem db_name --no-data --routines) <(mysqldump -hsystem2 db_name --no-data --routines) --side-by-side --suppress-common-lines --width=690 | more`
($) Check difference between two file directories recursively: `diff <(tree /dir/one) <(tree /dir/two)`

($) Produce three tab-separated columns: lines only in first file, lines only in second file and common lines: `comm file1 file2`
($) Print only lines common to both files: `comm -12 file1 file2`
($) Print only lines common to both files, reading one file from `stdin`: `cat file1 | comm -12 - file2`
($) Get lines only found in first file, saving the result to a third file: `comm -23 file1 file2 > file1_only`
($) Print lines only found in second file, when the files aren't sorted: `comm -13 <(sort file1) <(sort file2)`

AWK
===
($) Print the fifth column (a.k.a. field) in a space-separated file: `awk '{print $5}' filename`
($) Print the second column of the lines containing "foo" in a space-separated file: `awk '/foo/ {print $2}' filename`
($) Print the last column of each line in a file, using a comma (instead of space) as a field separator: `awk -F ',' '{print $NF}' filename`
($) Sum the values in the first column of a file and print the total: `awk '{s+=$1} END {print s}' filename`
($) Print every third line starting from the first line: `awk 'NR%3==1' filename`
($) Print different values based on conditions: `awk '{if ($1 == "foo") print "Exact match foo"; else if ($1 ~ "bar") print "Partial match bar"; else print "Baz"}' filename`
($) Print all lines where the 10th column value equals the specified value : `awk '($10 == value)' filename`
($) Print all the lines which the 10th column value is between a min and a max : `awk '($10 >= min_value && $10 <= max_value)'`
($) Print lines in a text file with numbers in first column higher or equal than a value: `awk '$NF >= 134000000 {print $0}' single-column-numbers.txt`
($) AWK calculator: `calc(){ awk "BEGIN{ print $* }" ;}; calc "((3+(2^3)) * 34^2 / 9)-75.89"`

PERL
====
($) Parse and execute a Perl script: `perl script.pl`
($) Check syntax errors on a Perl script: `perl -c script.pl`
($) Parse and execute a Perl statement: `perl -e perl_statement`
($) Run a Perl script in debug mode, using `perldebug`: `perl -d script.pl`
($) Loo[p] over all lines of a file, editing them [i]n-place using a find/replace [e]xpression: `perl -p -i -e 's/find/replace/g' filename`
($) Run a find/replace expression on a file, saving the original file with a given extension: `perl -p -i'.old' -e 's/find/replace/g' filename`
($) Run a multiline find/replace expression on a file, and save the result in another file: `perl -p0e 's/foo\nbar/foobar/g' input_file > output_file`
($) Run a regular expression on stdin, printing out the first capture group for each line: `cat path/to/input_file | perl -nle 'if (/.*(foo).*/) {print "$1"; last;}'`
($) Calculate the mean or average of a single column of numbers in a text file: `perl -lane '$total += $F[0]; END{print $total/$.}' single-column-numbers.txt`
($) Shuffle lines via perl: `seq 1 9 | perl -e 'print sort { (-1,1)[rand(2)] } <>'`
($) Shuffle lines via perl: `seq 1 9 | perl -MList::Util=shuffle -e 'print shuffle <>;'`

ZIP
===
($) Zip all JPG files in the current directory to a zip file: `zip images.zip *.jpg`
($) Zip multiple files and directories recursively: `zip -r archive.zip *.jpg ./mydir1 ./mydir2`
($) Zip but exclude everything in the private directory: `zip -r compressed.zip ./mydir -x '*private/*'`
($) Archive a directory and its contents with the highest level [9] of compression: `zip -r -9 compressed.zip ./mydir`
($) Create an encrypted archive (user will be prompted for a password): `zip -e -r compressed.zip path/to/directory`
($) Add files to an existing zip file: `zip compressed.zip path/to/file`
($) Delete all tmp files from the foo directory in an existing zip file: `zip -d compressed.zip "foo/*.tmp"`
($) Archive a directory and its contents to a multi-part split zip file (e.g. 3 GB parts): `zip -r -s 3g compressed.zip path/to/directory`
($) List files within a specified archive (without extracting them): `zip -sf compressed.zip`

($) Extract zip file(s) (for multiple files, separate file paths by spaces): `unzip file(s)`
($) Extract zip files(s) to given path: `unzip compressed_file(s) -d path/to/put/extracted_file(s)`
($) List the contents of a zip file without extracting: `unzip -l file.zip`
($) Extract the contents of the file(s) to stdout alongside the extracted file names: `unzip -c file.zip`
($) Extract a zip file created on Windows, containing files with non-ASCII (e.g. Chinese or Japanese characters) filenames: `unzip -O gbk file.zip`
($) Make a dedicated folder for each zip file: `for f in *.zip; do unzip -d "${f%*.zip}" "$f"; done`

GZIP
====
($) Compress a file, replacing it with a gzipped compressed version: `gzip file.ext`
($) Decompress a file, replacing it with the original uncompressed version: `gzip -d file.ext.gz`
($) Compress a file, keeping the original file: `gzip --keep file.ext`
($) Compress a file specifying the output filename: `gzip -c file.ext > compressed_file.ext.gz`
($) Decompress a gzipped file specifying the output filename: `gzip -c -d file.ext.gz > uncompressed_file.ext`
($) Specify the compression level. 1=Fastest (Worst): `gzip -9 -c file.ext > compressed_file.ext.gz`

($) Extract a file from an archive, replacing the original file if it exists: `gunzip archive.tar.gz`
($) Extract a file to a target destination: `gunzip --stdout archive.tar.gz > archive.tar`
($) Extract a file and keep the archive file: `gunzip --keep archive.tar.gz`
($) List the contents of a compressed file: `gunzip --list file.txt.gz`

BZIP2
=====
($) Compress a file: `bzip2 path/to/file_to_compress`
($) Decompress a file: `bzip2 -d path/to/compressed_file.bz2`
($) Decompress a file to standard output: `bzip2 -dc path/to/compressed_file.bz2`
($) Extracts blocks from damaged .bz2 files: `bzip2recover damaged_file_name`

XZ
==
($) Compress a file to the xz file format: `xz file`
($) Decompress a xz file: `xz -d file.xz`
($) Compress a file to the LZMA file format: `xz --format=lzma file`
($) Decompress an LZMA file: `xz -d --format=lzma file.lzma`
($) Decompress a file and write to stdout: `xz -dc file.xz`
($) Compress a file, but don't delete the original: `xz -k file`
($) Compress a file using the fastest compression: `xz -0 file`
($) Compress a file using the best compression: `xz -9 file`

TAR AND COMPRESSION
===================
($) List the contents of a tar file: `tar tvf source.tar`
($) Create an archive from files: `tar cf target.tar file1 file2 file3`
($) Create a gzipped archive from files: `tar czf target.tar.gz file1 file2 file3`
($) Create a gzipped archive from a directory using relative path: `tar czf target.tar.gz --directory=path/to/directory .`
($) Extract a (compressed) archive file into the current directory: `tar xf [source.tar.gz|.bz2|.xz]`
($) Extract a (compressed) archive file into the target directory: `tar xf [source.tar.gz|.bz2|.xz] --directory=directory`
($) Create a compressed archive from files, automatic suffix detection: `tar caf target.tar.xz file1 file2 file3`
($) Extract files matching a pattern: `tar xf source.tar --wildcards "*.html"`
($) Tar and bz2 a set of folders as individual files: `find . -maxdepth 1 -type d -name '*screenflow' -exec tar jcvf {}.tar.bz2 {} \;`
($) Create an uncompressed tar file of each child directory of the current working directory: `find . -maxdepth 1 -mindepth 1 -type d -exec tar cvf {}.tar {}  \;`

($) Zgrep across multiple files: `find . -name "file-pattern*.gz" -exec zgrep -H 'pattern' {} \;`

XORG
====
($) setxkbmap -option compose:ralt
($) Display information about key presses: `showkey -a`
($) Show information about a window by clicking on it: `xwininfo`
($) Send specific key strokes to a window: `xdotool`
($) Fetch specific window properties: `xprop`
($) Output text on to the clipboard: `echo "hello" | xclip -i -sel clip`
($) Get text from the clipboard: `xclip -o -sel clip`
($) List input devices: `xinput list` (e.g. to see Touchpad input on a laptop)
($) Disable touchpad (and possibly add to `.xprofile`): `xinput disable 'SynPS/2 Synaptics TouchPad'`
($) Set RGB gamma of secondary monitor: `secondscreen=$(xrandr -q | grep " connected" | sed -n '2 p' | cut -f 1 -d ' '); [ "$secondscreen" ] && xrandr --output $secondscreen --gamma 0.6:0.75:1`
($) Visual alert with keyboard LEDs: `for a in $(seq 16); do xdotool key Num_Lock;sleep .5; xdotool key Caps_Lock;done`
($) Change the size of a named window: `xdotool search --name "window name" windowsize 300 400`
($) Magnify a mouse-selected part of a screen: `slop | xargs xzoom -source`

MAN
===
($) Print the contents of a help file to stdout instead of pager: `man -Tutf8 networkmanager`
($) List all software that have a man page installed: `ls /usr/share/man/man1 2> /dev/null | sed -e 's/\.1.*//' | xargs whatis 2> /dev/null | sed -e '/unknown subject/d' -e 's/  *- / - /'`

