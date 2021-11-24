# LINUX SHELL COMMANDS

### GENERAL SHELL UTILITIES

Command | Action
--- | ---
!! | executes the last issued command
!$ | use the last argument
history | lists all the last issued commands
apropos <search phrase> | searches all programms for the specified search phrase
<command> \| column -t -s :	| displays stdout in a table with the seperator `:`
nohup <command> | executes the command, even if the user logs out, stdout gets written to nohup.out
<command> \| grep -c '<string>' | counts how often the specified string appears
<command> \| wc -l | counts the number of lines
ln <file> <link_name> | creates a hardlink to the file
ln -s <file> <link_name> | creates a softlink to the file
du -sh | show disk usage, summarized and human readable
tar -xvzf <your file> | extracts a .tar archive
export PATH=$PATH:path/to/dir | add a directory to the PATH

### Network/SSH

Command | Action
--- | ---
netstat	| shows all connections and ports used by the machine
netstat -tupln | shows tcp, udp, programms that are using the ports, listening ports and numeric (use sudo for PIDs)
ssh -M user@host | all further ssh connections will use the already established connection
ssh -p 22 user@host | use a specific port
scp user@server:/path/to/remote /path/to/local | copy file from remote to local
scp /path/to/local user@server:/path/to/remote | copy file from local to remote

### Create bootable media with `dd`
sudo dd if=path/to/file of=path/to/disk status=progress && sync

### Writing shell output to a file

Command | Action
--- | ---
command > file.txt | overwrite anything that is already inside the file
comand >> file.txt | append the output of the command to a file
\> file.txt | empty the specified file
command \| tee file.txt | this uses the tee command, which writes to a file and also displays the output of the command
command \| tee -a file.txt | use tee in append mode

In both cases, the file gets created, if it doesn't exist

### Chaining commands
command1 ; command2 | run command1 and then command2
command1 && command2 | run command2 only if command1 succeeds
command1 || command2 | run command2 only if command1 fails
yes || command | confirmes every action in a command or script
