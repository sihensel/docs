# LINUX SHELL COMMANDS

### General shell commands/shortcuts

Command | Action
--- | ---
`!!` | executes the last issued command
`!$` | use the last argument
`$?` | get the exit code of the last command
`history` | lists all the last issued commands
`apropos <search phrase>` | searches all programms for the specified search phrase
`<command> \| column -t -s :` | displays stdout in a table with the seperator `:`
`nohup \<command>` | executes the command, even if the user logs out, stdout gets written to nohup.out
`<command> \| grep -c '\<string>'` | counts how often the specified string appears
`<command> \| wc -l` | counts the number of lines
`ln <file> <link_name>` | creates a hardlink to the file
`ln -s <file> <link_name>` | creates a softlink to the file
`du -sh` | show disk usage, summarized and human readable
`tar -xvzf <your file>` | extracts a .tar archive
`export PATH=$PATH:path/to/dir` | add a directory to the PATH
`expand; unexpand` | convert tabs to spaces and vice versa
`join; split` | merge or split files
`sort` | sort the content of a file
`uniq` | removes all duplicate lines from a file
`grep -i` | ignore cases
`ps aux` | show all processes, including those without a tty
`kill -9 <PID>` | kill a process with signal 9
`jobs` | list all background processes that have been started with `&`
`fg %1` | bring the first background to to the foreground
`umask` | adjust default file permissions on new files


### Network/SSH

Command | Action
--- | ---
`netstat -tupln` | shows tcp, udp, programms that are using ports and listening ports (use sudo for PIDs)
`ssh -M user@host` | all further ssh connections will use the already established connection
`ssh -p 22 user@host` | use a specific port
`scp user@server:/path/to/remote /path/to/local` | copy file from remote to local
`scp /path/to/local user@server:/path/to/remote` | copy file from local to remote

### Create bootable media with `dd`
`sudo dd if=path/to/file of=path/to/disk status=progress && sync`

### Writing shell output to a file

Command | Action
--- | ---
`command > file.txt` | overwrite anything that is already inside the file
`command >> file.txt` | append the output of the command to a file
`> file.txt` | empty the specified file
`command \| tee file.txt` | this uses the tee command, which writes to a file and also displays the output of the command
`command \| tee -a file.txt` | use tee in append mode

In both cases, the file gets created, if it doesn't exist

### Chaining commands

Command | Action
--- | ---
`command1 ; command2` | run command1 and then command2
`command1 && command2` | run command2 only if command1 succeeds
`command1 || command2` | run command2 only if command1 fails
`yes \| command` | confirmes every action in a command or script

### Login shells

The `nologin` shell promts the user on login that the account is not available.  
`/bin/false` returns false (-1) and exits.  
`/bin/true` returns true (0) and exits.  
All three can be set as login shells, only nologin has the prompt.

### Writing to /dev/null
If the output of a command is not needed, it can be redirected to the null device `/dev/null`. This gets rid of all verbose output.  
`ping -c 3 archlinux.org >/dev/null`  
The command runs, displays no output and then exits. This is particularly useful in shell scripts, when the output of a command is not needed.

The streams `STDOUT` and `STDERR` can both be sent to `/dev/null`.  

```sh
command > /dev/null 2> /dev/null    # these are all the same
command 1> /dev/null 2> /dev/null
command > /dev/null 2>&1            # this is what's most seen
```

`/dev/null` can also be used for `STDIN` (0) if blank input is needed:
`command < /dev/null`

### File Permissions

The `s` bit states that the program always runs as root, even without sudo privileges (e.g. `/usr/bin/passwd`). SUID has the numerical value 4 (4755).
A `S` bit indicates a SUID bit without execute permissions.  
This also works for groups, where a program runs as another group (e.g. `/usr/bin/wall`). SGID has the numerical value 2 (2555).  
The sticky bit `t` means that anyone can write into a file or directory, but only root can delete it, such as `/tmp`. The sticky bit has the numerical value 1.

### Device types

Type | Explanation
--- | ---
b | block (transfer data in fixed blocks)
c | character (transfer data one character at a time)
p | pipe (allow transfer of data between processes)
s | socket (communication between multiple processes)
