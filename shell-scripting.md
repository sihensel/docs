# Shell Scripting

Use [spellcheck.net](https://www.shellcheck.net/) to check your script for errors!

## Comments

Comments are defined by a leading `#`. They start at the position of the `#`-symbol until the end of the line.
The _Shebang_ (see below) is also a comment.

```sh
# single line comment

:' Multiline comments
work like
this
```

## Shebang

The Shebang defines which shell the script uses.

```sh
#!/bin/bash
#!/usr/bin/env bash
#!/usr/bin/env sh
```

For example, `#!/bin/bash` tells your shellscript to use bash. Even better would be `#!/user/bin/env bash` as its uses the environemnt variable of the user instead of the binary. `bash` can be exchanged with `zsh`, `csh` or any other shell. If your script is POSIX compliant, you can use `#!/usr/bin/env sh`. This uses the deault shell of the user.

## Variables

### Defining Variables

Variables can be defined by assigning a value to a variable name. It is important to note, that there must be no spaces between the variable name, the `=` and the value, otherwise the shell interprets these as commands.

```sh
Variable=1
Variable="Hello World"
```

You can also define empty variables.

```sh
Variable=       # null
Variable=""     # empty string
```

### Working with Variables

When using the variable itself (assigning values or exporting), you use the variable name. When the value of a variable is needed, a `$`-symbol is used. 

```sh
echo $Variable
echo "$Varibale"
echo '$Variable'    # returns the string "$VARIABLE"
```

The first two `echo`-commands return the value of the variable, while the last `echo` returns the String `$Variable`. It is also advices to always use double quotes `"` around variables to avoid parameter expansion and other unexpected behavior.

### Parameter Expansion

Variables can be expanded, allowing their value to be modified during the time of the expansion.

```sh
Variable="Hello World"
echo ${$Variable}            # Hello World
echo ${Variable/World/You}  # Hello You
```

The example above replaces the first occurance of the string `World` with the string `You`.
The get the length of a string, use:

```sh
echo ${#Variable}   # 11
```

### Arrays

Arrays are not supportet by POSIX, but can be used in more modern shells like `bash` or `zsh`.
An array is defined with `()` and the elements are seperated by spaces.

```sh
array=(Hello World 1 2 3)
```

The elements of an array can be addressed in various ways:

```sh
echo "$array"           # Hello (first element)
echo "${array[0]}"      # Hello (first element)
echo "${array[@]}"      # Hello World 1 2 3 (whole array)
echo "${#array[@]}"     # 5 (number of elements)
```

### Brace extension { }

This can be used to generate a sequence of characters or numbers.

```sh
echo {1..10}
echo {a..z}
echo {A, B}.sh  # A.sh B.sh
```

## Control Flow

### If-Statements

If statements can be used to check for a specific condition and change the behavior of the script.
It is important to leave a space between the square brackets and the condition itself.

```sh
if [ "$Variable" = 10 ]; then
    echo "$Variable is 10"
else
    echo "$Variable is not 10"
fi
```

The logic parameters `and` and `or` are represented by `&&` and `||` respectively.
To use these in an if statement, you need to have multiple pairs of square brackets.

```sh
if [ "Contidion 1" ] || [ "Condition 2 "]; then
    # etc.
fi
```

Conditionals can also be used with shell commands.

```sh
git commit && git push
cd ~/.config/alacritty || exit 0
```

Bash also supports double square brackets `[[ ]]` that allow additional features found in more common programming languages, but these are at the same time not POSIX compliant.

### For-Loops

For-loops are use to iterate over a sequence of values.

```sh
for i in {1..5}; do
    echi "$i"
done
```

This can also be used with arrays.

```sh
for i in "${array[@]}"; do
    echo "$i"
done

# This will return the indices of the array
for i in "${!array[@]}"; do
    echo "$i"
done
```

## Funcitons

Functions take input parameters and return output parameters.
Note that the `function` keyword is not necessarily needed.

```sh
function myfunc() {
    # code here
}
```
