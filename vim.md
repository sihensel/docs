
# Vim

To get all the information about vim, use `vimtutor`.
This document lists all basic commands as a cheat sheet. For all commands, see `vimtutor`.



## Movement

To move around text, use `j`, `k`, `h` and `l`.

Key | Meaning
--- | ---
j | down
k | up
h | left
l | right
w | move one word forward
e | move to the end of the nect word forward
0 | jump to the start of the line
$ | jump to the end of the line
G | move to bottom of the file
gg | move to the start of the file
30G | move to line 30
% | move to correxponding bracket (), [], {}
H | move to top of screen
M | move to middle of screen
L | move to end of screen
} | move down on paragraph
{ | move up one paragraph
f<letter> | find the next letter
fa | find the next a
; | repeat last find command
F | reverse find command



## Tabs

Command | Meaning
--- | ---
:tabedit {file} | opens the specified file in a new tab
:tabclose | close current tab
:tabclose {i} | close the i-th tab
:tabonly | close all other tabs
:tabs | list current tabs
:tabm 0 | move current tab to first position
:tabm | move current tab to last position
:tabm {i} | move current tab to position i+1
gt | go to next tab
gT | go to previous tab
{i}gt | go to tab in position i

## Exit and Save

Command | Meaning
--- | ---
:q! | exit and discard all changes.
:w | save all changes
:w Test	| save the file with the name `Test`
:wq | save all changes and exit.



## delete and insert

Command | Meaning
--- | ---
x | delete letter under symbol
dw | delete word
d$ | delete to end of the line
dd | delete whole line
3dd | delete 3 lines
dap | delete the whole paragraph (same with cpoy, etc)
i | insert text at cursor
a | append text after the cursor
A | append text at end of the line
r | replace (press `r` and then the letter to replace, e.g. `ra` to replace with a)
ce | deletes word and places you in insert mode
cw | change word
c$ | change to the end of the line
o | opens new line below the cursos and places you in insert mode
O | opens a new line above the cursor



## Visual Mode

In Visual Mode, you can select text and then to different stuff with it (copy, replace, save to external file, etc).

Command | Meaning
--- | ---
v | enter visual mode
V | highlight whole lines
CTRL-V | highlight blocks
vap | highlight the while paragraph
:w Test | save highlighted area to `Test` (command must be executed in visual mode)

## Undo and Redo

Command | Meaning
--- | ---
u | undo
U | undo all changes to the line
CTRL-R | redo



## Copy and paste

`dd` saves the deleted line in a vim register, so you can paste them with `p`.

Command | Meaning
--- | ---
p | insert deleted lines under cursor
y | copy selection (yank)
yw | copies one word



## Cursor Location and File status

Command | Meaning
--- | ---
CTRL-G | show your location in the file and filestatus



## Search

__Note:__ The search in vim is case sensitive. To ignore this, use `:set ic`.  Do redo this, use `:set noic`

Command | Meaning
--- | ---
/ | search for phrase, from the cursor downwards
n | search for phrase again
N | search for phrase again, but upwards
? | search for phrase in upwards direction
CTRL-O | go back to where you came from



## External Commands

Command | Meaning
--- | ---
:!<command> | run external command
:!ls | runs the `ls` command
