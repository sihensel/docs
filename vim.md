# Vim

To get all the information about vim, use `vimtutor`.
This document lists all basic commands as a cheat sheet. For all commands, see `vimtutor`.

## Movement

To move around text, use `j`, `k`, `h` and `l`.

Key | Action
--- | ---
j | down
k | up
h | left
l | right
w | move one word forward
b | move one word backward
e | move to the end of the next word forward
0 | jump to the start of the line
$ | jump to the end of the line
gg | move to the start of the file
G | move to bottom of the file
30G | move to line 30
% | move to correxponding bracket (), [], {}
H | move to top of screen
M | move to middle of screen
L | move to end of screen
} | move down on paragraph
{ | move up one paragraph
CTRL-b | page up
CTRL-f | page down
CTRL-u | half page up
CTRL-d | half page down
f<letter> | find the next letter
fa | find the next a
; | repeat last find command
F | reverse find command
( | move to sentence begin, upwards
) | move to sentence begin, downwards
\+ | next line
\- | previous line
CTRL-y | scroll one line up
CTRL-e | scroll one line down
zb | scroll cursor to bottom
zz | scroll cursor to center
zt | scroll cursor to top

## Tabs

Command | Action
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

Command | Action
--- | ---
:q! | exit and discard all changes
ZQ | exit and discard all changes
:w | save all changes
:w Test	| save the file with the name `Test`
:wq | save all changes and exit
ZZ | save all changes and exit

## Delete and Insert

Command | Action
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
C | change to the end of the line
c$ | change to the end of the line
o | opens new line below the cursos and places you in insert mode
O | opens a new line above the cursor
s | substitute a character
S | substitute the whole line

## Visual Mode

In Visual Mode, you can select text and then to different stuff with it (copy, replace, save to external file, etc)

Command | Action
--- | ---
v | enter visual mode
V | highlight whole lines
CTRL-V | highlight blocks
vap | highlight the while paragraph
:w Test | save highlighted area to `Test` (command must be executed in visual mode)

## Undo and Redo

Command | Action
--- | ---
u | undo
U | undo all changes to the line
CTRL-R | redo

## Copy and paste

`dd` saves the deleted line in a vim register, so you can paste them with `p`.

Command | Action
--- | ---
p | insert deleted lines under cursor
y | copy selection (yank)
yw | copies one word

## Commands in Normal Mode

Command | Action
--- | ---
CTRL-g | show your location in the file and filestatus
CTRL-a | increases the number under the cursor by 1
CTRL-x | decreases the number under the cursor by 1

## Search

__Note:__ The search in vim is case sensitive. To ignore this, use `:set ic`.  Do redo this, use `:set noic`

Command | Action
--- | ---
/ | search for phrase, from the cursor downwards
? | search for phrase in upwards direction
n | search for phrase again
N | search for phrase again, but in reverse direction
CTRL-o | go back to where you came from
CTRL-i | go forth to where you came from
\* | search for the word under the cursor
\# | reverse search for the word under the cursor

## External Commands

Command | Action
--- | ---
:!<command> | run external command
:!ls | runs the `ls` command

## g-Commands
Command | Action
--- | ---
gj | moves a visual line, not a line in the editor (useful when text wraps, can be used with k as well)
g0 | move to beginning of visual line
g$ | move to end of visual line
gq\<text-object> | add returns into a textblock so wrapped lines become real lines (eg. gq$ to end of line)
gu\<test-object> | uncapitalize object
guu | uncapitalize the whole line
gU\<text-object> | capitalize object
~ | toggle capitalization for one character
g~\<text-object> | toggle capitalization for the object
gf | open the file that is selected by the cursor (^ to return)
gv | jump to the last selected text
J | join lines
gJ | join lines without spaces between the joined lines
g& | apply the last substitution command to the whole file

## :-Commands
Command | Action
--- | ---
:s/text-to-replace/replacement/g | find and replace all occurences of a string in the whole line
:%s/old-string/replacement/g | find and replace all occurences of a string in the whole file
:split \<file> | open a file in horizontal split mode
:vs \<file> | open a file in vertical split mode
