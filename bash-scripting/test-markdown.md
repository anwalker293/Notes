# Shell Scripting Notes

Keyboard Shortcuts

- `G`
    
    Go to end of file
    

Command Shortcuts (`:`)

- `set nu`
    
    Displays line numbers
    
    To make persistent, add command to `~/.vimrc`
    
- `set nonu`
    
    Disable line numbering
    

Knowledge Checks

- 

## Section 01: Intro to Bash Shell Scripting

---

- Bash Aliases
    - List all aliases
        
        `alias`
        
    - Run original command and not alias
        
        `\ls`
        
    - Create a new alias
        
        `alias alias_name='command_to_run'`
        
        ex. `alias c='clear'`
        
    
    *In order to make aliases persistent, need to write to either `~/.bash_profile` or `~/.bashrc`*
    
    - Load the configuration file
        
        `source .bashrc`
        
    - Remove an alias
        
        `unalias *name*`
        
        e.g. `unalias now`
        
    
- Shells and Scripts
    - Other Shells
        - BOURNE
        - BASH (Bourne Again SHell)
        - Z Shell
        - C Shell
        - List all valid other shells
            
            `cat /etc/shells`
            
    - Change User’s Default Shell
        
        `chsh -s $PATH`
        
        where, in the `$PATH`, you are putting the path to the new shell (e.g. `/bin/bash`)
        
    - See default shell of a user
        
        `cat /etc/passwd`
        
    - fkgn
- Execution Permissions
    - Add
        - For owner
            
            `chmod 700 name.sh`
            
        - For all users
            
            `chmod +x name.sh`
            
    - Remove
        
        `ls -l *name*`
        
- Shebang
    
    `#!(ABSOLUTE_PATH_OF_SHELL_TYPE)`
    
    e.g. Bash Shebang `#!/bin/bash` (also used by default)
    
    e.g. Python Shebang `#!/usr/bin/python3`
    
- Comments in Bash
    
    Ignores everything on the line of `#`
    
    - Multi-line comment
        
        ```bash
        : '
        hey look at me what's up
        ls -a
        '
        ```
        
- Running Scripts
    - Script executed in new subshell
        
        `./script.sh`
        
        *Need to move to script’s directory and set execution permissions*
        
    - Script executed in current shell
        
        `source script.sh`
        
        or, e.g.
        
        `bash script.sh`
        

### Knowledge Checks

- How can you print the value of the current shell that has started?
    
    `echo $SHELL`
    

---

## Section 02: Variables

---

**Naming Conventions**

- Use lower_case variable names
- Constants should be written in CAPITALS
- Variable names should be descriptive
- Variable names cannot start with a number and cannot contain spaces or other special characters

---

- Variables in Bash
    
    `os=Linux` (spaces are not allowed)
    
    `distro="Kali Linux"`
    
    - Bash is **weakly-tied** programming language
        
        *Does not require you to define any data type at the time of data declaration*
        
    - List all shell variables
        
        `set`
        
        - To look for a specific variable
            
            `set | grep $NAME`
            
            e.g. `set | grep distro`
            
    - Remove a variable
        
        `unset $NAME`
        
        e.g. `unset distro`
        
    - Make a variable read-only
        
        `declare -r LOGDIR=/var/log`
        
        If you try to change the value of `LOGDIR`, you will get an error
        
- Variable Expansion and Quoting
- Environment and Shell Local Variables
- Getting User Input
- Positional Parameters
- Project: How to protect yourself from hackers
- Special Parameters
- $@ vs. $*

---

## Section 03: Shell Expansions

---

- Intro. to Expansions
- Brace Expansion
    - String Lists
    - Sequence Lists
    - Project: Brace Expansion
- Tilde, Parameter, and Variable Expansions
- Command Substitution
- Arithmetic Expansion
- Process Substitution
- Word Splitting
- Filename Expansion (Globbing)

## Section 04: Shell Operation in Depth

- Intro. to Shell Operation
- Tokenization
- Command Identification
- Shell Expansions
- Quote Removal
- Redirections (StdIn, StdOut, StdErr)
- Hands-On Example

## Section 05: Program Flow in Bash

- If, Elif, Else
- Arithmetic Comparisons