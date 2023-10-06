---
title: "CLI: Handbook"
date: 2023-10-06T21:32:14+05:30
tags: ["cli", "shell", "bash", ]
author: ["Me"]
showToc: true
TocOpen: false
hidemeta: false
comments: false
summary: "CLI practical handbook of most used CLI commands" 
description: "CLI practical handbook of most used CLI commands"
disableHLJS: true
disableShare: false
disableHLJS: false
hideSummary: false
searchHidden: false
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
---


This cheat sheet-style guide provides a quick reference to commands and practices commonly used when working Command Line (CLI). This cheat sheet mostly uses `bash` or `zsh`.

> [explainshell](https://explainshell.com/) demystify the complex CLI commands. [cheat.sh](https://cheat.sh/) and [tldr.sh](https://tldr.sh/) are also amazing resource for references. 

### Documentation
* `man` - By Default the system documentation/manuals are documented in the man pages
```shell
# Usage:
man <COMMAND>
```

* `help` - Lists all the internal commands of shell
```shell
# Usage:
help
```
### Bash structure
* `~/.bashrc` - configure the shell, define command aliases and set command shell options
* `~/.bash_profile` - initialization commands that set environment variables, a shell's prompt
* `~/.bash_logout` - cleanup operations and other commands that you want the shell to execute whenever a user logs out of a shell
* `~/.bash_aliases` - if storing many aliases commands

### Shell Script
```shell
set -x # Enable RAW i/o
set -v # Enable RAW i/o 
set -e # abort on error(non-zero exit code)
set -u # detect unset variables
variable=${VARIABLE_NAME:-default} # default value if variable empty
$(( (1+1)/5 )) # Arthematic operation
${1..10} # Sequence
${var%suffix} # trim a suffix named 'suffix'
${var#prefix} # trim a prefix named 'prefix'
${foo,bar} # expand into multiple values
diff /etc/hosts < (ssh somehost cat /etc/hosts) # parenthesis to execute a sub-process
{..} # Include code in between this block, as a good practice

cat <<EOF
touch somefile
echo foo > somefile
EOF


# Bash Script strict mode
#!/bin/bash
set -euo pipefile
IFS=$'\n\t'
```

### Redirection
`File Descriptors` - every file has an associated number called FD. Default ones are
* `0` - STDIN
* `1` - STDOUT 
* `2` - STDERR

`Types of redirection`
* `>` - STDOUT redirection to file, `overwrites at destination`
```shell
# Usage:
ls -lrt > test.out
```
* `>>` - STDOUT redirection to file, `appends at destination` 
```shell
# Usage:
ls -lrt >> test.out
```
* `<` - STDIN redirection from a file
```shell
# Usage:
wc < input_file.out
```
* `File Descriptors` based redirection
```shell
# Usage:
# Redirect the STDERR to STDOUT
ls -lrt 2>&1
```
* `|` - redirects STDOUT of one command into the STDIN of a second command
```shell
# Usage:
# Redirect the STDOUT to STDIN
ls -lrt | wc
```
### Navigation
* `Ctrl + a` - Go to beginning
* `Ctrl + e` - Go to end
* `Alt + b` - Go back one word
* `Alt + f` - Go forward one word
* `Ctrl + w` - Delete a word backward
* `Alt + d` - Delete next word
* `Ctrl + u` - Delete to beginning of line OR delete line
* `Ctrl + k` - Delete to end
* `Ctrl + r` - Search backwards(recursive)
* `Ctrl + s` - Search forwards
* `Ctrl + l` - clears the screen
* `Alt + t` - transpose the last 2 words/characters

### Job Management
* `&` - background process.
* `ctrl+z` - pauses the process temporarily, and places it in `SUSPENDED` mode.
* `fg` - used to bring a background / paused process back to the foreground and continue its execution.
* `bg` - resume any `SUSPENDED` jobs and run them in background
* `jobs` - check the job status of all the jobs
* `kill` - used for killing a process
* Status: SUSPENDED, CONTINUED, RUNNING, STOPPED

```shell
# Usage:
sleep 100 & # Run the command as a backgroud process
jobs # view all the jobs
jobs -l # view all the jobs along with their job ID & process PID
fg %<JOB_ID> # Resume the job in fore-ground which has the JOB_ID
bg %<JOB_ID> # Resume the job in the back-ground which has the JOB_ID
ctrl+z # Pauses executing of the current running job
ctrl+c # terminates the current executing process
```

### SecureShell - SSH
* TCP/IP port 22
* `sshd` - ssh server
  * package `openssh-server`
  * default sshd server config `/etc/ssh/sshd_config`
* `ssh` - ssh client
  * package `openssh-client`
  * default ssh client config `/etc/ssh/ssh_config` or `~/ssh/config`
```shell
# Usage:
# -v = verbose mode for debugging
# -4 = forces IPv4
# -6 = forces IPv6
# -i = specify the identity private keys
# -p = port

ssh user@HOST
ssh -i private_key_file user@HOST
ssh HOST # if the ssh keys are already copied in the server
```
* Generating the SSH keys - `ssh-keygen`
```shell
# Usage:
# -t = type of key, dsa, ecdsa, ecdsa-sk, ed25519, ed25519-sk, rsa, rsa-sha2-512(DEFAULT)
# -b = Key length
# -f = Specify file to write to
# -C = comment
ssh-keygen -t rsa -b 4096
ssh-keygen -t ed25519
```
* Securely copying public SSH keys - `ssh-copy-id`
```shell
# Usage:
# -i = Specify the ssh keys file
# -p = Port to login
ssh-copy-id -i ~/.ssh/id_rsa.pub user@HOST
```
* `scp` & `sftp` 

### Other Linux Commands
* `ls` - listing of files
```shell
# Usage:
# --color = enabling coloured output
# -l = long listing
# -h = human readable Listing of data
# -t = sort by time
# -R = Recursive listing of directories

# Must use aliases - `.bash_aliases`
alias ls='ls -A -F -B --human --color'
alias ll='ls -l'
alias la='ls -A'
alias lh='ls -h'

ls -lrt
```
* `less` - terminal pager
```shell
# Usage:
# -N = Line number enabled
# +F = Real Time Monitoring

sudo dmesg | less +F
cat test.txt | less
```
* `head` - starting content of the files
```shell
# Usage:
# -n = line numbers to be displayed in the head
# -c = bytes to be displayed from the file

head file.txt
head -n 10 file.txt
head -c 150 file.txt
```
* `tail` - end of the content of files
```shell
# Usage:
# -f = real time updates to the files content appended to the end of file.
# -n = no of lines of tail to be displayed

tail file.txt
tail -f file.log
```
* `ln` - soft/hard link
```shell
# Usage:
# -s = softlink/symlink

ln sourceFileForHardLink hardLinkFileNameWithPath
ln -s sourceFileForHardLink softLinkFileNameWithPath
```
* `chmod` - change file permissions
  * User-Group-Others(--- --- ---)
  * Read-Write-Execute(rwx)
```shell
# Usage:
# -R = Recursive
# -V = Verbose

chmod +x file.txt
chmod 600 file.txt
chmod u-w file.txt
chmod u+x file.txt
```
* `chown` - change the ownership of files
```shell
# Usage:
# -R = Recursive
# -V = Verbose

chown user:group directory
```
* `du` - directory usage
```shell
# Usage:
# -h = human readable

du -h
```
* `df` - disk filesystem
```shell
# Usage:
# -h = human readable
# -i = inode listing
# --total = total summary
# --output = specify the output strategy

df -h /proc
df -h --output=source,avail,pcent,target
```
* inode - index number
  * Data structures that keeps track of all the files in the filesystem
  * all unique inode in a given filesystem 
  * stores metadata(type, size, group, user, permission, access, change & modification time) 
* `grep` - search for string
```shell
# Usage:
# --color = color for search results
# -r = recursive search
# -i = ignore case search
# -c = count the matching lines
# -E = search extended using regex
# -v = invert the search 
# -n = add the line numbers to the search results
# -w = exact search 
# -o = just show the matched strings, not the entire line
grep "test" test_file.txt
grep "key" *
```
* `find` - find the files/directories etc
```shell
# Usage:
# -name = file the name
# -iname = file name search using ignorecase
# -type = type of search, d = directory, f = file
#         l = symbolic link, s = socket, b = block special
# -exec = command execute the command in searched files 
# -user = search the file with the specified user
# -size = size of files to be searched

find . -name somefile.txt
find /home -iname somefile.txt
find / -type f -perm 0777 -print -exec chmod 644 {} \;
```
* `ps` - lists all the processes which are running. 
```shell
# Usage:
# -A or -e = list all the process running for all users 
# -T = process running with the current terminal(tty)
# -f = full listing for the process
# -u = filter process for a specific user 
# -L = List threads for a process PID
# -G = List process associated with a group
# -C = search process by name as PID is not known
# -o = specifies the format for the columns

ps -ef
ps aux
ps -L <PID>
```
* `nohup` - run process in the background, even when session is logged out, "no hangup": prevents from receiving SIGHUP signals
```shell
# Usage:
nohup ./example.sh > output.log 2>&1 &
nohup python example.py &
```
* `disown` - delete/remove jobs so that it doesn't send SIGHUP signal 
```shell
# Usage:
# -a = removes all the jobs
# -h = marks job so that it DOESN'T send SIGHUP when shell receives a SIGHUP signal
# -r = deletes running job

disown -r
disown -h %2
disown -a
```
* `kill` - kills the running process
```shell
# Usage:
# -l = lists all the SIG for kill
#   1 - HUP - reload a process
#   9 - KILL - Force kill a process
#   15 - TERM - Graceful kill of process 

kill PID_NUMBER
kill -9 PID
```
* `fzf` - CLI fuzzy finder
* `rg` - ripgrep - recursive grep
* `pandoc` - Convert documents
```shell
#Usage
pandoc README.md --from markdown --to docx -o tmp.docx
```
* `sort` - alphabetical(lowercase < uppercase) / increasing sorting by default
```shell
#Usage
# -r = reverse order
# -n = recognise numerical results
# -k = sort based on specific column
# -u = remove duplicate entries
# -c = check if the file is in sorted format
# -M = arrange based on months
# -s = stable sort

sort -nr numeric.txt
ls -l | sort -nk 5
sort -c test.txt
```
* `uniq` - remove duplicate entries, must be in sorted format
```shell
#Usage
# -c = count freq 
# -d = print only the lines which are duplicated 
# -u = print all unique lines
# -i = check the frequency after ignoring the case

sort file2 | uniq -c
sort file2 | uniq -u
sort file2 | uniq -cd
```
* `cut` - cut a field out
```shell
# Usage:
# -f = field to cut
# -d = delimiter to use for cutting 

cut -d: -f1,6 /etc/passwd
ls -lrt | cut -d ' ' -f 1
```
* `wc` - word count
```shell
# Usage:
# -l = no of lines
# -w = no of words
# -m = no of characters

wc -l
```
* `tr` - translate character
```shell
# Usage:
# -C = Complement character
# -d = delete character
tr A-Z a-z
tr -d 'e'
```
* `sed` - substitution
```shell
sed s/Amazing/super/gi textfile.txt
sed s/originalword/replaceword/g filename
sed -n '3,5p' textfile.txt
```
* `rsync` - remote sync
```shell
# Usage
# -v = verbose
# -r = recursive
# -z = compress during transfer
# -h = human-redable
# -P or --progress = shows real-time progress

rsync -avzhe ssh --progress /root/pkgs root@127.0.0.1:/root/pkgs
rsync -zvh backup.tar.gz /tmp/backups/
```
* `curl` - request URLs
```shell
# Usage
# -o = save data to a file
# -L = redirect follow
# -C = resume an interrupted download
# -u = authentication
# -v = verbose
# --headers = specify request headers
# -d or --data = POST request body
# -X = specify the request type
# -k = disable certificate checking

curl -o logo.png https://reqbin.com/static/img/logo.png
curl --header 'Host: targetapplication.com' https://192.0.0.1:8080/
curl -X POST https://httpbin.org/post
```
* Operations with compressed files - `zless`, `zmore`, `zcat`, `zgrep`
* `nc` & `telnet` & `netstat`
```shell
# Usage
nc -lv 1234 # start a listener on port 1234
nc -zv google.com 443 # check if port is open
nc -zv 10.0.2.4 1230-1235 # scanning a port range
telnet host.com 1234 # connect to the port in the HOST
netstat -an | grep LISTEN
```
