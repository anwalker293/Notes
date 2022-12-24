# Shell Scripting Notes

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
        
- Environment and Shell Local Variables
    - **Environment Variables**
        - are inherited by any child shells or processes
        - used to pass information to processes that are spawned from the current shell
        - displayed using `env` or `printenv`
        - Grab multiple variables from `printenv`
            
            `printenv HOME SHELL PWD`
            
        
        `env | grep HOME` = `printenv HOME`
        
        - Create new environment variable
            
            `name=var` pair in `/etc/environment`
            
            - Create for user
                
                `export VAR="hello"` in configuration file (e.g. `.bashrc`)
                
            - Create for all users
                
                `export VAR="hello"` in `/etc/profile` or `/etc/bash.bashrc`
                
    - **Shell Variables**
        - are contained exclusively within the shell in which they were set
        - displayed using `set`
        - Info about `HIST`
            
            Only displayed by `set`, not by `env` comment
            
            - `HISTFILE`
                
                Path/name of the file where command history is saved
                
                Default is `~/.bash_history`
                
            - `HISTFILESIZE`
                
                Maximum number of lines contained in history file
                
            - `HISTSIZE`
                
                Maximum number of commands to be remembered in history list
                
        - Have `set` not print out commands
            
            `set -o posix`
            
            `set`
            
- Getting User Input
    
    `read name`
    
    - Creates new variable called name
    - Whatever input the user puts will be the value stored to name
    - `read name age city`
        
        `Alex 22 Tirana`
        
        *Each variable can be accessed sep*a*rately*
        
    
    *If no variable is supplied, value stored in `$REPLY`*
    
    - `read -p "Enter the IP Address:" ip`
        
        prompt
        
    - `read -s -p "Enter password:" pswd`
        
        Makes user input silent
        
- Positional Parameters
    - Parameters referenced by a name are called variables
    - …by a number are called **positional parameters**
    - …by a special symbol are called **auto-set parameters**
    - `./script.sh filename.txt directory1 10.0.0.01`
        - `$0`
            
            Name of the script itself (`script.sh`)
            
        - `$1`
            
            Name of first positional parameter (`filename.txt`)
            
- Project: How to protect yourself from hackers
    
    ```bash
    #!/bin/bash
    
    read -p "Enter the IP Address or domain to drop:" ip
    
    echo "Blocking connections from $ip"
    sleep 1
    
    iptables -I INPUT -s $ip -j DROP
    echo "Done"
    ```
    
    Or, to make it as an optional parameter,
    
    ```bash
    #!/bin/bash
    
    echo "Blocking connections from $1"
    sleep 1
    
    iptables -I INPUT -s $1 -j DROP
    echo "Done"
    ```
    
- **Special Parameters**
    - `$0`
        
        The name of the script itself 
        
    - `$@` and `$*`
        
        A string representation of all positional parameters
        
    - `$#`
        
        The number of positional arguments
        
    - `$?`
        
        The most recent foreground command exit status
        
    - `$$`
        
        The process ID of the [invoking] shell
        
- `$@` vs. `$*`
    
    Only differ when wrapped inside double quotes
    
    - `$@` Word splitting is performed
        
        `$@="$1 $2 $3`
        
    - `"$@"` Word splitting is not performed
        
        `"$@"="$1" "$2" "$3"`
        
    - **Example of `$@` vs. `"$@"`**
        
        ```bash
        (create-files.sh)
        #!/bin/bash
        
        touch $@   # touch "$1 $2 $3"
        touch "$@" # touch "$1" "$2" $3"
        ```
        
        `./create-files.sh "my file.txt" "your report.txt" "his document.txt"`
        
        - The first set will produce six files: `my`, `file.txt`, `your`, `report.txt`, `his`, `document.txt`
        - The second set will produce three: `"my file.txt" "your report.txt" "his document.txt"`
    - Internal Field Separator (IFS) is by default white space
    - `$*` is the same as `$@` (unquoted)
    - `"$*"` will place the first character of the IFS variable between parameters
    - **Example of `$*` vs. `"$*"`**
        
        ```bash
        (create-files.sh)
        #!/bin/bash
        
        IFS=,
        touch $*     # equivalent to touch $@
        touch "$*" 
        ```
        
        `./create-files.sh "my file.txt" "your report.txt" "his document.txt"`
        
        - The first set will produce six files: `my`, `file.txt`, `your`, `report.txt`, `his`, `document.txt`
        - The second set will produce one: `"my file.txt,your report.txt,his document.txt"`

---

### Challenges

- **Challenge 2**
    - Create a script and define two string variables.
    - Add a comment line after the shebang and another inline comment.
    - Create a new variable by concatenating the two string variables.
    - Run the script.
    - Solution
        
        ```bash
        #!/bin/bash
        # Simple script to concatenate two vars
        var1="variable1"
        var2="variable2"
        var3="${var1}${var2}" # Concatenate the two strings here
        echo $var3
        ```
        
- **Challenge 3**
    - [Consider the following script](https://drive.google.com/file/d/1Y0Fe6vQWxVm0FlTuFAEfTC3Opln2CYz5/view?usp=sharing) that contains some variable declarations with errors.
    - Fix the script so that it runs without any errors.
    - Solution
        
        ```bash
        #!/bin/bash
         
        year = 1991
        release_month="September"
        day=17
        
        echo "Linux was first released on $release_month $day, $year."
        ```
        
- **Challenge 4**
    - Create a Bash script that when run with arguments displays the number of arguments and the value of each argument.
    - Run the script with 10 arguments.
    - Solution
        
        ```bash
        #!/bin/bash
        
        # Display the number of arguments
        echo "Number of arguments: $#"
        
        # Display value of each argument
        for item in $@
        do
        	echo "Value of item is $item"
        done
        ```
        
- **Challenge 5**
    
    • Create a script that clears the contents of a text file which is the script’s argument.
    
    - Solution
        
        ```bash
        #!/bin/bash
        echo "" > $1 
        
        # equivalent to:
        # cat /dev/null > $1
        
        echo "$1 contents was cleared!"
        ```
        
- **Challenge 6**
    - Create a script that drops the packets from an IP address given by the user.
    - Example: the command to drop the packets from 100.0.0.1 is: **iptables -I INPUT -s 100.0.0.1 -j DROP**
    - Solution
        
        ```bash
        #!/bin/bash
        
        read -p "Enter the IP Address or domain to drop:" ip
        
        echo "Blocking connections from $ip"
        sleep 1
        
        iptables -I INPUT -s $ip -j DROP
        echo "Done"
        ```
        
- **Challenge 7**
    
    • Change the [solution from the previous challenge](https://drive.google.com/file/d/1_BxQAYZY0cJ9sieuEBPZW15Q1SrYbpAf/view?usp=sharing) so that the IP address is given as the first argument of the script.
    
    - Solution
        
        ```bash
        #!/bin/bash
        
        echo "Blocking connections from $1"
        sleep 1
        
        iptables -I INPUT -s $1 -j DROP
        echo "Done"
        ```
        
- **Challenge 8**
    - Create a script that takes a string as an argument.
    - If the string is empty (the script is run without an argument), the string will take the default value **aaa.**
    - Create a directory and file inside it with the names of the argument of the script.
    - Solution
        
        ```bash
        #!/bin/bash
        
        # If $1 is supplied, keep; if not supplied, default to aaa
        echo "Creating directory ${1:-aaa} ..."
        mkdir ${1:-aaa}
        
        echo "Creating file ${1:-aaa}/${1:-aaa} ..."
        touch ${1:-aaa}/${1:-aaa}
        
        echo "Done!"
        ```
        
- **Challenge 9**
    
    • Create a script called **fix_permissions.sh** that prompts the user for a directory and changes recursively, the permissions of all subdirectories to 755 and of all files to 644.
    
    - Solution
        
        ```bash
        #!/bin/bash
        
        read -p "Enter directory: " dir
        
        echo -n "Changing subdirectories permissions to 755 recursively..."
        find $dir -type d -exec chmod 755 {} \;
        echo "Done"
        
        echo -n "Changing files permissions to 644 recursively..."
        find $dir -type f -exec chmod 644 {} \;
        echo "Done"
        ```
        
- **Challenge 10**
    - Create a script called **files_owned_by_user.sh** that prompts the user for a directory and a username.
    - The script will find and display the names of all regular files (not directories) in that directory owned by the user.
    - Solution
        
        ```bash
        #!/bin/bash
        
        read -p "Enter a directory:" dir
        read -p "Enter a username:" username
        
        echo "Regular files owned by $username in $dir ... "
        find $dir -type f -user $user -ls
        ```
        
- **Challenge 11**
    
    • Change the solution to the previous challenge so that the directory is the first argument of the script and the user the second argument of the script.
    
    - Solution
        
        ```bash
        #!/bin/bash
        
        echo "Regular files owned by $2 in $1 ... "
        find $1 -type f -user $2 -ls
        ```
        
- **Challenge 12**
    - Develop a Bash script that creates a new group and a new username. The group will be the user’s secondary group.
    - The username and the group will be given as input by the user that runs the script.
    - The script will also display the last 2 lines from the files where the users and the groups are saved in Linux.
    - Test the script by running it as root.
    - Solution
        
        ```bash
        #!/bin/bash
        
        read -p "Enter the group to create: " group
        read -p "Enter the user to create: " username
        
        groupadd $group
        useradd -s /bin/bash -m -G $group $username
        
        tail -n 2 /etc/group
        echo 
        tail -n 2 /etc/passwd
        echo
        
        echo "The group $group and the username $username were created!"
        sleep 1
        ```
        
- **Challenge 13**
    - Consider the [solution to the previous challenge](https://drive.google.com/file/d/1lNX5-CkklNGck2tAJkco_5DWZMWdthlW/view?usp=sharing).
    - Change the script in such a way that the user and the group that are created will be the arguments of the script. The user will be the first argument of the script and the group will be the second argument of the script.
    - Test the script by running it as root.
    - Solution
        
        ```bash
        #!/bin/bash
        
        groupadd $2
        useradd -s /bin/bash -m -G $2 $1
        
        tail -n 2 /etc/group
        echo 
        tail -n 2 /etc/passwd
        echo
        
        echo "The group $2 and the username $1 were created!"
        sleep 1
        ```
        

---

## Section 03: Shell Expansions

---

### Command Line Processing

1. Tokenization
2. Command Identification
- **Shell Expansions**
    1. Brace expansion
    2. Tilde expansion
    3. Parameter and variable expansion
    4. Command substitution
    5. Arithmetic expansion
    6. Process substitution
    7. Word splitting
    8. Filename expansion (Globbing)
1. Quote Removal
2. Redirections
3. Command Execution

---

- Brace Expansion
    - ***No spaces allowed before or after the commas in a string list***
        
        Else, it will be ran as a normal command line argument
        
        - `echo 01-{old,current,new,backup}.txt`
            
            *01-old.txt 01-current.txt 01-new.txt 01-backup.txt*
            
        - `echo 01-{old,current,new,{10,20,30}-backup}.txt`
            
            *01-old.txt 01-current.txt 01-new.txt 01-10-backup.txt 01-20-backup.txt 01-30-backup.txt*
            
        - `echo {1..20..2}`
            
            1 3 5 … 19
            
        - `{a..k}`
            
            “a”, “b”, “…”, “k”
            
        - `{01..10}`
            
            01, 02, 03, 09, 10
            
        - `file{01..15}.txt`
        - `Day{1..7}`
        - `{z..a}`
    - ***Brace expansion happens before variable expansion***
    - Project: Brace Expansion
        - Make directories for January, February, and March
        - Make subdirectories, one for each day of the month (31 for Jan, 28 for Feb, 31 for March)
        - Put daily log files in each subdirectory
        - Solution
            
            ```bash
            #!/bin/bash
            
            sudo mkdir -p /var/log/app/{January/{01..31},February/{01..28},March{01..31}}
            sudo touch /var/log/app/{January/{01..31}/log.daily,February/{01..28}/log.daily,March{01..31}/log.daily}
            ```
            
- Tilde, Parameter, and Variable Expansions
    - `{os^^}`
        
        Will make value of os capitalized
        
    - `{os,,}`
        
        Will make value of os lowercase
        
    - `~+`
        
        Expands to the value of the current working directory (pwd)
        
    - `~-`
        
        Expands to the value of the oldpwd/old current working directory
        
    - `${1:-aaa}`
        
        Substitutes default value to `aaa` if `$1` is not provided
        
    - `${1:=aaa}`
        
        Even if you put something in for `$1`, the value gets reassigned
        
- Command Substitution
    
    **Syntax:** ``command`` or `$(command)`
    
    - Examples
        - `sudo tar -czf etc-$(data +%F_%H%M).tar.gz /etc/ 2> /dev/null`
            
            Creates a new etc-~.tar.gz file, with the command in `$()` writing the current date, hour and minute within the file name
            
- Arithmetic Expansion
    
    **Syntax**: `$(( example ))`
    
    - Examples
        - `let y=2**128`
            
            ^Actually results in an overflow error, returns `y=0`
            
    
    ***Arithmetic Expansion is done only using integers***
    
    If you want to use floating point numbers, use `bc` (basic calculator)
    
    - Examples
        - `echo "3*7" | bc`
        - `echo "11/4" | bc`
            
            Actually only returns 2 (need to specify scale if you want more)
            
        - `echo "scale=2;11/4" | bc`
            
            2.75
            
        - `bc <<< "scale=3;23/7`"
- Process Substitution
    
    To reference the output of a process as a file
    
    - `cat <(ls)`
        
        Prints out contents of current directory
        
    - `diff <(ls dir1) <(ls dir2)`
        
        Shows the differences between files/subdirs in dir1 and dir2
        
- Word Splitting
    
    IFS by default is a white space
    
    Bash only performs word splitting on unquoted expansions
    
    Let `dirs="d1 d2 d3"`
    
    - `mkdir $dirs`
        
        Will create **three** directories: `d1`, `d2`, `d3`
        
    - `mkdir "$dirs"`
        
        Will create **one** directory: `d1 d2 d3`
        
- Filename Expansion (Globbing)
    - Globbing Characters
        - `*`
            
            
        - `?`
            
            Matches any single character
            
        - `[]`
            
            Matches a single character from within a range
            
    - `ls fa[123].txt`
    - `ls fa[a-c][3-7].txt`
    - `ls f[^ab]*` or `ls f[!ab]`*
        
        Will list all files that start with f and are NOT followed by a or b
        

---

### Challenges

- **Challenge 1**
    - Using a brace expansion, create a script that displays odd numbers between 1 and 50.
    - Solution
        
        ```bash
        #!/bin/bash
        
        echo "{1..50..2}"
        ```
        
- **Challenge 2**
    - Using a brace expansion, develop a script that will create 5 text files in the current directory.
    - The names of the files should be **a.txt, b.txt, c.txt, d.txt** and **e.txt.**
    - Solution
        
        ```bash
        #!/bin/bash
        
        touch {a..e}.txt
        ```
        
- **Challenge 3**
    - Declare a variable called **processes** that contains all the running processes of the system.
    - Solution
        
        ```bash
        #!/bin/bash
        
        processes="$(ps aux)"
        echo "$processes"
        ```
        
- **Challenge 4**
    - Declare a variable called **users** that contains all the users of the system.
    - Use a command substitution.
    - Solution
        
        ```bash
        #!/bin/bash
        
        users="$(cut -d: -f1 /etc/passwd)"
        ```
        
- **Challenge 5**
    - Run a single command that creates a file with the current date as its name and contains the current date and time.
    - Use command substitutions.
    - Solution
        
        ```bash
        #!/bin/bash
        
        echo "$(date)" > "$(date +%F).txt"
        ```
        
- **Challenge 6**
    - Calculate and display the result of **27 * 48**
    - Do the task in 3 different ways.
    - Solution
        
        ```bash
        #!/bin/bash
        
        echo "27*48" | bc
        echo "scale=4;27*48" | bc
        bc <<< "scale=4;27*48"
        ```
        
- **Challenge 7**
    - Create a script that prompts the user for a distance in miles.
    - Convert and display the distance in km with 3 decimals. Note that 1 mile = 1.609 km.
    - Solution
        
        ```bash
        #!/bin/bash
        
        read -p "Enter distance (in miles): " distance_mi
        
        # Grab value in km
        km=$(echo "scale=3;$distance_mi*1.609"|bc)
        echo "That is $km km."
        ```
        
- **Challenge 8**
    - Using **cat** and a process substitution display all the running processes.
    - Solution
        
        ```bash
        #!/bin/bash
        
        cat <(ps -ef)
        ```
        
- **Challenge 9**
    - Using **ls** and a globbing expression display all the files in **/etc** that end in **.conf**
    - Solution
        
        ```bash
        #!/bin/bash
        ls /etc/*.conf
        ```
        
- **Challenge 10**
    - Using **ls** and a globbing expression display all the filenames in **/var/log** that start with a letter (lower or uppercase) and end in **log**
    - Solution
        
        ```bash
        #!/bin/bash
        ls -l /var/log/[a-zA-Z]*log
        ```
        

---

## Section 04: Shell Operation in Depth

---

### Command Line Processing

1. Tokenization
2. Command Identification
- **Shell Expansions**
    1. Brace expansion
    2. Tilde expansion
    3. Parameter and variable expansion
    4. Command substitution
    5. Arithmetic expansion
    6. Process substitution
    7. Word splitting
    8. Filename expansion (Globbing)
1. Quote Removal
2. Redirections
3. Command Execution

---

- Tokenization
    
    Bash uses **metacharacters** to break the command line into tokens (words and operators)
    
    - **Metacharacters:** `space`, `tab`, `newline`, `|`, `;`,`&`,`()`,`<>`
    - Token Types
        1. **Words** (don’t contain any unquoted metacharacters)
        - 2. **Operators** (contain at least one unquoted metacharacter)
            
            *Operators only matter if they are unquoted*
            
            - Control
                
                `newline`, `|`,`||`, `&`,`&&`, `;`,`;;`, `;&`,`;;&`, `|&`,`()`
                
            - Redirection
                
                `>`, `>>`,`>|`, `<>`,`>&`, `&>`, `<<`,`<<<`, `<<-`,
                
- Command Identification
    
    Breaks down into simple and compound commands
    
    - `touch a.txt b.txt; ls`
    - Reserved Words
        
        Reserved words *start a compound command*
        
        *Examples: `if`, `for`, `[[`, `]]`, `time`, etc.*
        
        - `type if`
            
            Shell keyword
            
- Shell Expansions
- Quote Removal
    - All unquoted occurrences of backslashes (`\`), single quotes (`’’`), and double quotes (`””`) that DID NOT results from an expression are removed
- Redirections (StdIn, StdOut, StdErr)
    
    Data Streams Attached to Each Command:
    
    1. `STDIN (0)` ⇒ Standard Input (`<`)
    2. `STDOUT (1)` ⇒ Standard Output (`>,>>`)
    - `tty`
        
        File representing the terminal (`/dev/pts/0`)
        
    1. `STDERR (2)` ⇒ Standard Error (`2>, 2>>`)
    - Example (2)
        
        `tail -n 3 /etc/shadow` will result in a permission denied error. 
        
        - `tail -n 3 /etc/shadow 2>error.txt`
            
            Redirects errors to `error.txt`
            
    - Example (1) (2)
        
        `tail -n 2 /etc/passwd /etc/shadow > output.txt 2> errors.txt`
        
    - Write stdout and stderr to same file
        
        `tail -n 2 /etc/passwd /etc/shadow > output_errors.txt 2>&1`
        
    - `ifconfig | grep ether | cut -d" "-f10 > mac.txt`
        
        Finds lines with ether in ifconfig, grabs mac address
        

---

## Section 05: Program Flow in Bash

---

- If, Elif, Else
    
    Syntax:
    
    ```bash
    if [some]
    then
    	[some]
    elif [some]
    then
    	[some]
    else
    	[some]
    fi
    ```
    
    - Example
        
        ```bash
        #!/bin/bash
        if [ -f "$1" ] # Use double quotes to not be subject to word splitting
        then           # aka allow for file names with spaces
        	echo "The argument is a file, displaying its contents"
        	sleep 1
        	cat $1
        elif [ -d "$1" ]
        then
        	echo "The argument is a directory, running ls -l ..."
        	sleep 1
        	ls -l $1
        else
        	echo "The argument ($1) is neither a file or a directory"
        fi
        ```
        
- Arithmetic Comparisons
    - `man test`
        
        Load all comparison operators
        
    - Example
        
        ```bash
        #!/bin/bash
        read -p "Enter your age:" age
        
        if [[ $age -lt && $age -ge 0 ]]
        then
        	echo "You are minor!"
        elif [[ $age -eq 18 ]]
        then
        	echo "Congrats, you've just become major!"
        elif [[ $age -gt 18 && $age -le 100 ]]
        	echo "You are major!"
        else
        	echo "Invalid age"
        fi
        ```
        
    - Logal Operators
        - Logical AND —> `&&` for double square brackets, `-a` for `[]`
        - Logical OR —> `||` for double square brackets, `-o` for `[]`
- Multiple Condition Tests and Nested if/then Statements
    - Example
        
        ```bash
        #!/bin/bash
        
        if [[ $# -eq 1 ]]
        then
        	if [[ $age -lt && $age -ge 0 ]]
        	then
        		echo "You are minor!"
        	elif [[ $age -eq 18 ]]
        	then
        		echo "Congrats, you've just become major!"
        	elif [[ $age -gt 18 && $age -le 100 ]]
        		echo "You are major!"
        	else
        		echo "Invalid age"
        	fi
        else
        	echo "This script should be run with an argument"
        fi
        ```
        
- String Comparisons
    - Comparison Operators
        - `=` for `[]` and `==` for double square brackets
            - Example
                
                ```bash
                #!/bin/bash
                read -p "String 1:" str1
                read -p "String 2:" str2
                
                if [ "$str1"="$str2" ] 
                then
                	echo "The strings are equal"
                else
                	echo "The strings are not equal"
                fi
                
                if [[ "$str1"=="$str2" ]]
                then
                	echo "The strings are equal"
                else
                	echo "The strings are not equal"
                fi
                
                if [[ "$str1" != "$str2" ]];then
                	echo "The strings are not equal"
                fi
                ```
                
            - Substring Example
                
                ```bash
                #!/bin/bash
                str1="This substring contains the word Linux yay"
                if [[ "$str1"==*"Linux"* ]]
                then
                	echo 'The substring "Linux" is there'
                else
                	echo 'The substring "Linux" is not there'
                fi
                ```
                
        - `-n` → The string length is non-zero
        - `-z` → The string length is zero
            - Example usage of `-n` and `-z`
                
                ```bash
                #!/bin/bash
                my_str="abc"
                if [[ -z "$my_str" ]]
                then
                	echo "The string is of zero length"
                else
                	echo "The string is not of zero length"
                fi
                
                if [[ -n "$my_str" ]]
                then
                	echo "The string is not zero length"
                else
                	echo "The string is zero length"
                fi
                ```
                
- Project: Testing Network Connections
    
    ```bash
    #!/bin/bash
    
    output="$(ping -c $1)"
    if [[ $output == *"100% packet loss"* ]]
    then
    	echo "The network connection to $1 is not working!"
    else
    	echo "The network connection to $1 is working!"
    fi
    ```
    
- The Case Statement
    
    Simple, more elegant form of `if, elif` statements
    
    - Syntax
        
        ```bash
        #!/bin/bash
        case "$variable1" in 
        pattern1);
        	# Execute code
        	;;
        pattern2);
        	# Execute code
        	;;
        patternN);
        	# Execute code
        	;;
        *);
        	# Execute code 
          # (run if no pattern matches/final pattern/default case)
        	;;
        esac
        ```
        
    - Example
        
        ```bash
        #!/bin/bash
        
        echo -n "Enter your favorite pet:"
        read pet
        case "$pet" in 
        "dog");
        	echo "You like dogs"
        	;;
        "cat" | "Cat" | "kitty" );
        	echo "You like cats"
        	;;
        fish | "African Turtle");
        	echo "Fish or turtles are great!"
        	;;
        *);
        	echo "Your favorite pet is unknown"
        	;;
        esac
        ```
        
    - Another Example (choose between two arguments)
        
        ```bash
        #!/bin/bash
        if [[ $# -ne 2 ]]
        then
        	echo "Run the script with two arguments: Signal and PID."
        	exit 1
        fi
        
        case "$1" in
        1)
        	echo "Sending the SIGHUP signal to $2"
        	kill -SIGHUP $2
        	;;
        2)
        	echo "Sending the SIGINT signal to $2"
        	kill -SIGINT $2
        	;;
        15)
        	echo "Sending the SIGTERM signal to $2"
        	kill -15 $2
        	;;
        *)
        	echo "Signal no. $1 will not be delivered"
        	;;
        esac
        ```
        
    - `kill -l`
        
        Lists all the signals a process can receive
        
- Menus in Bash (The Select Statement)
    
    The `select` statement provides us with a dropdown menu to select from
    
    - Syntax
        
        ```bash
        #!/bin/bash
        PS3="Select an option:"
        
        select ITEM in list_of_options:
        do
        	COMMANDS
        done
        ```
        
    - Example
        
        ```bash
        #!/bin/bash
        
        PS3="Choose your country:"
        select COUNTRY in Germany France USA "United Kingdom" Quit
        do
        	# $REPLY is the number user enters from menu
        	case $REPLY in 
        	1)
        		echo "You speak German!"
        		;;
        	2)
        		echo "You speak German!"
        		;;
        	3)
        		echo "You speak American English!"
        		;;
        	4)
        		echo "You speak British English!"
        		;;
        	5)
        		echo "Quitting..."
        		sleep 1
        		exit
        		;;
        	*)
        		echo "Invalid option $REPLY"
        		;;
        	esac
        done
        ```
        
- Project: System Administration Script Using Menus
    
    ```bash
    #!/bin/bash
    PS3="Your choice:"
    select ITEM in "Add User" "List All Processes" "Kill Process" "Install Program" "Quit"
    do
    	if [[ $REPLY -eq 1 ]]
    	then
    		read -p "Enter the username:" username
    		# Search for the username, double-check that it doesn't
    		# already exist
    		output="$(grep -w $username /etc/passwd)"
    		if [[ -n "$output" ]]
    		then
    			echo "The username already exists!"
    		else
    			# Create the username
    			sudo useradd -m -s /bin/bash "$username"
    
    			# Check successful
    			if [[ $? -eq 0 ]]
    			then
    				echo "The user $username was added successfully"
    				# Print last line of the passwd file to see newly created user
    				tail -n 1 /etc/passwd
    			else
    				echo "There was an error adding the user"
    			fi
    
    		fi
    
    	elif [[ $REPLY -eq 2 ]]
    	then
    		echo "Listing all processes"
    		sleep 1
    		ps -ef
    
    	elif [[ $REPLY -eq 3 ]]
    	then
    		read -p "Enter the process to kill:" process
    		pkill $process
    
    	elif [[ $REPLY -eq 4 ]]
    	then
    		read -p "Enter the program to install:" app
    		sudo apt update && sudo apt install $app -y
    
    	elif [[ $REPLY -eq 5 ]]
    	then
    		echo "Quitting..."
    	fi
    
    	else
    		echo "Invalid menu selection."
    	
    done
    
    ```
    
- List of Commands
    - List Operators:
        - `;`
            
            Allows you to use commands right after one another
            
        - `&`
            
            Executes command in the background asynchronously
            
            - `sleep 10& ls`
                
                Does not wait for `sleep 10` to finish before running `ls`
                
        - `&&`
            
            Command 2 runs iff Command 1 runs with success
            
        - `||`
            
            Runs Command 2 **iff Command 1 errors**
            

---

### Challenges

- **Challenge 1**
    - Create a script that takes an argument and tests if it's a file or not.
    - Solution
        
        ```bash
        #!/bin/bash
        
        echo "Enter file to check:" file
        if [ -f "$file" ]
        then         
        	echo "The argument is a file"
        elif [ -d "$file" ]
        then
        	echo "The argument is a directory"
        else
        	echo "The argument ($file) is neither a file or a directory"
        fi
        ```
        
- **Challenge 2**
    - Consider the [solution to the previous challenge](https://drive.google.com/file/d/1xr0pnl-Jl7sv8PmddXR2apcaeJC3fStI/view?usp=sharing). Change it so that it also tests if the script is run with exactly one argument.
    - Solution
        
        ```bash
        #!/bin/bash
        
        if [[ $# -eq 1 ]]
        then
        	if [ -f "$1" ]
        	then         
        		echo "The argument is a file"
        	elif [ -d "$1" ]
        	then
        		echo "The argument is a directory"
        	else
        		echo "The argument ($1) is neither a file or a directory"
        	fi
        else
        	echo "The script was run with $# arguments. Please run with one argument"
        fi
        ```
        
- **Challenge 3**
    - Create a script that clears the contents of a text file which is the script’s argument.
    - Test if the argument is of type regular file.
    - Solution
        
        ```bash
        #!/bin/bash
        
        if [[ -f "$1" ]]
        then
        	cat "" > "$1"
        	echo "The contents of $1 was cleared!"
        else
        	echo "The script's argument should be a regular file!"
        fi
        ```
        
- **Challenge 4**
    - Develop a script that appends to a file the users that are currently logged in and the current date and time.
    - Solution
        
        ```bash
        #!/bin/bash
        
        users="`who | cut -f1 -d" "`"
        date_time="`date +%F\ %H:%M`"   #\ to escape the whitespace
        
        echo $date_time >> logged_users.txt
        echo "-------------------------" >> logged_users.txt
        echo $users >> logged_users.txt
        echo >> logged_users.txt
        ```
        
- **Challenge 5**
    - Create a script that prompts for a username, checks if the username exists on the system and displays "User exists!" or "User DOES NOT exist!".
    - Solution
        
        ```bash
        #!/bin/bash
        
        read -p "Enter a username to check for:" username
        
        result="$(grep -w $username /etc/passwd)"
        if [[ -n $result ]]
        then
        	echo "User ($username) exists"
        else
        	echo "User ($username) does not exist"
        fi
        ```
        
- **Challenge 6**
    - Create a script that prompts the user for a file. Test that the file exists and is of type regular file.
    - Prompt the user for 2 positive integers n and m.
    - Display the lines (from line number n to m, both included) of the text file.
    - Solution
        
        ```bash
        #!/bin/bash
        
        read -p "Enter a file to check for: " file
        if [[ -f $file ]]
        then
        	read -p "Enter a positive integer: " n
        	read -p "Enter another consecutive positive integer: " m
        	while [[ $n -lt 0 || $m -lt 0 || $n -gt $m ]]
        	do
        		if [[ $n -lt 0 || $m -lt 0 ]]
        		then
        			echo "Hey! No. Please try again TWO POSITIVE integers."
        			sleep 1
        		fi
        
        		if [[ $n -gt $m ]]
        		then
        			echo "The integers must be consecutive, sorry"
        			sleep 1
        		fi
        
        		read -p "Please enter a positive integer: " n
        		read -p "Please enter another consecutive positive integer: " m
        	done
        
        	echo "Displaying lines $n to $m of the file ($file)..."
        	sleep 1
        	q=$((m-n+1)) # no. of lines to display
        	tail -n "+$n" $file | head -n $q
        else
        	echo "Are you sure $file is a file?"
        fi
        ```
        
- **Challenge 7**
    - Create a script that takes the name of a process as an argument, checks if that process is running and displays "Process is running!" or "Process IS NOT running!".
    - Check that the script is run with exactly one argument.
    - Solution
        
        ```bash
        #!/bin/bash
        if [[ $# -ne 1 ]]
        then
        	echo "The script must be run with exactly one argument!"
        	exit 10
        fi
        
        output="$(pgrep $1)"
        if [[ -n $output ]]
        then
        	echo "Process \"$1\" is running!"
        
        else
        	echo "Process \"$1\" IS NOT running!"
        fi
        
        ```
        
- **Challenge 8**
    - Create a script that prompts the user for a character.
    - If the character is **Y,** display **"YES"**. If the character is **N,** display **"NO".** Use double quotes when displaying **YES** and **NO**. If the user enters any other characters, exit the script.
    - Solution
        
        ```bash
        #!/bin/bash
        
        cat <(ps -ef)
        ```
        
- **Challenge 9**
    - Change the previous script this way: If the character is **Y** or **y** display **"YES"**. If the character is **N** or **n,** display **"NO"**. If the user enters any other characters, exit the script.
    - Use the OR operator (||).
    - Solution
        
        ```bash
        #!/bin/bash
        
        echo -n "Enter Y or N: "
        read c
        if [[ "$c" == "Y" || "$c" == "y" ]]
        then
        	echo "\"YES\""
        elif [[ "$c" == "N" || "$c" == "n" ]]
        then
        	echo "\"NO\""
        else
        	echo "You entered another character. Exiting ..."
        	exit
        fi
        ```
        
- **Challenge 10**
    - Change the solution from the previous challenge and use **case** instead of **if...elif..else**.
    - Solution
        
        ```bash
        #!/bin/bash
        
        echo -n "Enter Y or N: "
        read c
        case $c in
        "Y" | "y")
        	echo "\"YES\""
        	;;
        "N" | "n")
        	echo "\"NO\""
        	;;
        *)
        	echo "You entered another character. Exiting ..."
        	;;
        esac
        ```
        
- **Challenge 11**
    - Create a script that takes exactly two arguments. The first argument is a shell command and the second argument is a string.
    - The script will check if the string belongs to the output of the command.
    - Solution
        
        ```bash
        #!/bin/bash
        if [[ $# -ne 2 ]]
        then
        	echo "Run the script with exactly two arguments."
        	exit
        else
        	output="`$1`"
        	if [[ "$output" == *"$2"* ]]
        	then
        		echo "$2 is contained within the output of $1"
        	else
        		echo "$2 is NOT contained within the output of  $1"
        	fi
        fi
        ```
        
- **Challenge 12**
    - **Create a Bash Menu with the following entries:**
        - *Display Date and Time*
        - *List users logged in*
        - *Display disk usage*
    - Change the Menu Prompt to the string **Choose:**
    - Implement each entry in the menu using the **if...elif..else** statements.
    - Solution
        
        ```bash
        #!/bin/bash
        PS3="Choose: "
        select ITEM in "Display date and time" "List users logged in" "Display disk usage"
        do
        
        	case $REPLY in
        	1)
        		echo "Displaying date and time"
        		sleep 1
        		date
            sleep 1
            exit
        		;;
        	2)
        		echo "Listing users logged in"
        		sleep 1
        		who -aH
            sleep 1
            exit
        		;;
        	3)
        		echo "Displaying disk usage"
        		sleep 1
        		df -h
            sleep 1
            exit
        		;;
        	*)
        		echo "Unknown selection"
            sleep 1
            exit
        		;;
        	esac
        done
        ```
        
- **Challenge 13**
    - **Create a Bash Menu with the following [menu entries](https://drive.google.com/file/d/1X799twxp_aO8Ke3A4nMIg3lLaQxaNy-9/view?usp=sharing).**
    - Implement each entry in the menu.
    - The outcome of your final solution should be similar to [this one](https://drive.google.com/file/d/1N2R1W-3nlMcH9zEovMCMtHWqyMwVFYCC/view?usp=sharing).
    - Solution
        
        ```bash
        #!/bin/bash
        PS3="Enter your choice [1-9]: "
        select ITEM in "Operating System Info" "Network Info" "Public IP" "Online Users" "CPU Info" "RAM Info" "Disk Usage" "Processes" "Quit"
        do
        	case $REPLY in
        	1)
        		echo "#########################"
        		echo "Operating System Info"
        		echo "#########################"
        		sleep 1
        		echo "Kernel version: $(uname -r)"
        		if [[ -f /etc/lsb-release ]]
        		then
        			cat /etc/lsb-release
        		fi
            exit
        		;;
        	2)
        		echo "#########################"
        		echo "Network Info"
        		echo "#########################"
        		sleep 1
        		echo "------------------------"
        		echo "Interface Info"
        		echo "------------------------"
        		sleep 1
        		ip address
        		echo 
        		echo "------------------------"
        		echo "Default Gateway"
        		echo "------------------------"
        		sleep 1
        		netstat -r
        		echo 
        		echo "------------------------"
        		echo "Traffic Statistics"
        		echo "------------------------"
        		sleep 1
        		netstat -i
        		echo "#########################"
            exit
        		;;
        	3)
        		echo "#########################"
        		echo "Public IP"
        		echo "#########################"
        		sleep 1
        		curl ident.me
        		echo 
        		echo "#########################"
            exit
        		;;
        	4)
        		echo "#########################"
        		echo "Online Users"
        		echo "########################"
        		who -H
        		echo "########################"
            exit
        		;;
        	5)
        		echo "#########################"
        		echo "CPU Info"
        		echo "#########################"
        		sleep 1
        		lscpu
        		echo "#########################"
            exit
        		;;
        	6)
        		echo "#########################"
        		echo "RAM Info"
        		echo "#########################"
        		echo "------------------------"
        		echo "Free and used RAM"
        		echo "------------------------"
        		sleep 1
        		free
        		echo 
        	
        		echo "------------------------"
        		echo "Virtual memory statistics"
        		echo "------------------------"
        		sleep 1
        		vmstat
        		
        		echo
        		echo "------------------------"
        		echo "Top 5 memory eating processes"
        		echo "------------------------"
        		sleep 1
        		ps auxf | sort -nr -k 4 | head -5  
        		echo "------------------------"
            exit
        		;;
        	7)
        		echo "#########################"
        		echo "Disk Usage"
        		echo "#########################"
        		sleep 1
        		df -H | grep -v "tmpfs"
        		echo
            exit
        		;;
        	8)
        		echo "#########################"
        		echo "Processes"
        		echo "#########################"
        		echo "------------------------"
        		echo "All running processes"
        		echo "------------------------"
        		sleep 1
        		ps -ef
        	
        		echo "------------------------"
        		echo "Top 5 memory eating processes"
        		echo "------------------------"
        		sleep 1
        		ps auxf | sort -nr -k 4 | head -5  
        		echo "------------------------"
            exit
        		;;
        	9)
        		echo "Quitting..."
        		sleep 1
            exit
        		;;
        	*)
        		echo "Unknown selection"
            sleep 1
            exit
        		;;
        	esac
        done
        ```
        

---

## Section 06: Loops in Bash

---

- For Loops
    - Syntax
        
        ```bash
        #!/bin/bash
        for item in list_of_values
        do
        	RUN_THESE_COMMANDS
        done
        ```
        
    - Example
        
        ```bash
        #!/bin/bash
        for os in Ubuntu Pop!_OS Slackware Kali
        do
        	echo "os is $os"
        done
        ```
        
    - Example (over a range of numbers)
        
        ```bash
        #!/bin/bash
        for num in {3..7}
        do
        	echo "num is $num"
        done
        ```
        
    - Example (display contents of all files in current directory)
        
        ```bash
        #!/bin/bash
        # Loop through items in current directory
        for item in ./*
        do
        	if [[ -f $item ]]
        	then
        		echo "Displaying the contents of $item"
        		sleep 1
        		cat $item
        	fi
        done
        ```
        
    - Example (rename files ending in .txt)
        
        ```bash
        #!/bin/bash
        for file in *.txt
        do
        	mv "$file" "renamed_by_script_$file"
        done
        ```
        
    - Example (Display numbers)
        
        ```bash
        #!/bin/bash
        numbers="1 3 6 7 129"
        for n in $(echo $numbers);do
        	echo -n "$n"
        done
        ```
        
    - Example (Like enumerate, but only when i≤20)
        
        ```bash
        #!/bin/bash
        for ((i=0;i<=20;i++))
        do
        	echo "i=$i"
        done
        ```
        
- Project: Dropping a List of IP Addresses Using a For Loop
    
    ```bash
    #!/bin/bash
    # ips.text is a text file containing all ips to drop
    for ip in $(cat ips.txt)
    do
    	echo "Dropping packets from $ip"
    	iptables -I INPUT -s $ip -j DROP
    done
    ```
    
- While Loops
    - Syntax
        
        ```bash
        #!/bin/bash
        while CONDITION
        do
        	RUN_THESE_COMMANDS
        done
        ```
        
    - Example
        
        ```bash
        #!/bin/bash
        i=0
        while [[ $i -lt 10 ]]
        do
        	echo "i=$i"
        	(( i++ ))
        	# above is same as let i=i+1
        done
        ```
        
    - Example (infinite loop)
        
        ```bash
        #!/bin/bash
        while [[ 1 -eq 1 ]]
        do
        	echo "Infinite loop, press CTRL+C to exit"
        done
        ```
        
    - Example (checking if process is running)
        
        ```bash
        #!/bin/bash
        while true
        do
        	output="$(pgrep -l $1)"
        	if [[ -n $output ]] # if not empty
        	then
        		echo "The process \"$1\" is running"
        	else
        		echo "The process \"$1\" is NOT running"
        	fi
        	sleep 3
        done
        ```
        
    - Example (checking ping is successful)
        
        ```bash
        #!/bin/bash
        while ping -c 1.8.8.8.8
        do
        	echo "Ping was successful"
        	sleep 1
        done
        ```
        
    - Example
        
        ```bash
        #!/bin/bash
        while read line
        do
        	echo #line
        done< /etc/passwd # (or $1) # redirects this file to the while loop
        ```
        
- Project: Dropping a List of IP Addresses Using While and Process Substitution
    
    ```bash
    #!/bin/bash
    while read ip
    do
    	echo "Dropping packets from $ip"
    	iptables -I INPUT -s $ip -j DROP
    	sleep 0.5
    done < ./ips.txt # redirects this to the while loop
    # ^Can also do
    # done< <(cat ips.txt)
    ```
    
- Loops with Break and Continue
    - With **break**, you can exit from a `for`, `while`, `until`, or `select` loop
        - Example
            
            ```bash
            #!/bin/bash
            i=0
            while [[ $i-lt 7 ]]
            do
            	echo "i is $i"
            	(( i++ ))
            	if [[ $i -eq 3 ]]
            	then
            		echo "i is 3, so exiting the loop"
            		break
            	fi
            done
            ```
            
        - Example
            
            ```bash
            #!/bin/bash
            while do
            do
            	ping -c 1 $1 &> /dev/null
            	if [[ $? -eq 0 ]]
            	then
            		echo "OK"
            	else
            		echo "Exiting the while loop"
            		break
            	fi
            	sleep 1
            done
            echo "Ping to $1 is not working, I'm sending an email to the admin"
            ```
            
    - The **continue** statement skips the remaining commands inside the loop for the current iteration and passes program control to the next iteration of the loop
        - Example (print out all odd numbers)
            
            ```bash
            #!/bin/bash
            for i in {1..15}
            do
            	result=$(( i%2 ))
            	if [[ $result -eq 0 ]]
            	then
            		continue
            	fi
            	echo $i
            done
            
            ```
            
- Bash Arrays
    - Bash provides one-dimensional indexed and associative arrays
        - Associative arrays contain key: value pairs
            - Uses a key to map the value instead of an index position
    - Example
        
        ```bash
        #!/bin/bash
        ages=(20 22 40 38)
        echo $age
        echo ${ages[@]}  # print whole 
        echo ${ages[*]}  # print whole
        echo ${!ages[*]} # print indices
        echo ${#ages[*]} # number of elements in array
        echo ${ages[1]}
        ```
        
    - Another example
        
        ```bash
        #!/bin/bash
        numbers[0]=100
        numbers[1]=200
        echo ${numbers[@]}
        echo {$!numbers[@]}
        ```
        
    - Example
        
        ```bash
        #!/bin/bash
        declare -a names
        names[0]="Dan"
        names[1]="Alina"
        names[2]="Diana"
        echo ${names[@]}
        names[7]="Maya"
        echo ${names[@]}
        unset ${names[@]}
        ```
        
    - Example (Appending, slicing)
        
        ```bash
        #!/bin/bash
        years=(2018 2019 2020 2021 2022)
        years+=(2023) # append
        years+=(2024 2025 2026)
        echo ${years[@]:2}
        # Only four elements starting with **2*
        echo {$years[@]:2:4}
        ```
        
    - Example (Associative Arrays)
        
        ```bash
        #!/bin/bash
        declare -A userdata
        userdata[username]="youradmin"
        userdata[password]="fdsa4"
        userdata[uid]=1010
        echo ${userdata[username]}
        echo ${userdata[@]}
        echo ${!userdata[@]} # print keys
        userdata[login]="$(date --uts +%s)"
        userdata+=([shell]="Bash" [admin]="False") # append
        # Make READONLY array
        declare -r -A SUPERSTARS=(
        [Germany]="Boney M"
        [USA]="Bon Jovi"
        [England]="The Beatles"
        )
        ```
        
- Using the Readarray Command
    - Converts whatever it reads into a standard array
    - Example
        
        ```bash
        readarray months
        Jan
        Feb
        Mar
        Apr
        ^C
        readarray months < < (cat months.txt)
        ```
        
    - `echo ${months[@]@Q}`
        
        Sees \n as a part of the array
        
    - Another example (with all the users of the system)
        
        ```bash
        #!/bin/bash
        readarray users< <(cut -d: -f1 /etc/passwd)
        echo ${users[@]}
        readarray -t files< < (les /etc)
        ```
        
- Iterating Over Arrays (for bloop in bloop_array)
    - Example (display contents of all files)
        
        ```bash
        #!/bin/bash
        readarray -t files < <(ls /etc/*)
        for f in "${files[@]}"
        do
        	if [[ -f $f && -r $f ]] # if file and can read
        	then
        		cat $f
        	fi
        done
        ```
        
- Project: Account Creation
    
    ```bash
    #!/bin/bash
    readarray accounts< <(cat ./users.txt)
    
    for account in ${accounts[@]}"
    do
    	user=$(echo $account | cut -d: -f1)
    	group=$(echo $account | cut -d: f2)
    
    	if [[ -z "$(grep -w /etc/group)" ]]
    	then
    		groupadd $group
    		echo "Group $group added successfully!"
    	else
    		echo "Group $group already exists!"
    	fi
    	
    	if [[ -z "$(grep -w $user /etc/passwd)" ]]
    	then
    		useradd -G $group $user
    		echo "User $user added successfully!"
    	else
    		echo "User $user already exists!"
    	fi
    	echo "###############"
    	sleep 1
    done
    ```
    

---

### Knowledge Checks

- How can you print the value of the current shell that has started?

---

### Challenges

- **Challenge 1**
    - Write a script that displays 20 random numbers less than 100.
    - A random number (integer) can be generated directly by reading from the **$RANDOM** variable.
    - Solution
        
        ```bash
        #!/bin/bash
        n=0
        while [[ $n -lt 20 ]]
        do
        	r=$RANDOM
        	if [[ $r -lt 100 ]]
        	then
        		echo $r
        		(( n++ ))
        	fi
        done
        ```
        
- **Challenge 2**
    - Change the previous script so that it displays **n random numbers** where **n** is the first argument of the script.
    - The generated numbers should be less than 1000 and **even**.
    - A random number (integer) can be generated directly by reading from the **$RANDOM** variable.
    - Solution
        
        ```bash
        #!/bin/bash
        n=0
        while [[ $n -lt $1 ]]
        do
        	r=$RANDOM
          if [[ $r -lt 1000 && $(($r%2)) == 0 ]]
        	then
        		echo $r
        		(( n++ ))
        	fi
        done
        ```
        
- **Challenge 3**
    - Consider the following variable: friends="Anne John Jenna Arena Paul Dan"
    - Create a script that uses a simple **for** loop and says **Hello** to each friend in the list.
    - Solution
        
        ```bash
        #!/bin/bash
        
        friends=("Anne" "John" "Jenna" "Arena" "Paul" "Dan")
        for item in "${friends[@]}"
        do
        	echo "Hello $item!"
        done
        ```
        
- **Challenge 4**
    - Create a script that displays odd numbers between 1 and 50.
    - Use a while loop.
    - Solution
        
        ```bash
        #!/bin/bash
        
        n=1
        while [[ n -lt 50 ]]
        do
        	echo "$n"
        	let n=n+2
        done
        ```
        
- **Challenge 5**
    - Develop a script that uses a for loop and creates 5 text files in the current directory.
    - Solution
        
        ```bash
        #!/bin/bash
        
        for item in {1..5}
        do
        	touch $item.txt
        done
        ```
        
- **Challenge 6**
    - Develop a script that prompts the user for a number **n** and then creates **n** text files in the current directory.
    - Solution
        
        ```bash
        #!/bin/bash
        
        read -p "Enter number of text files to create: " n
        while [[ $i -lt $n ]]
        do
        	touch "$i.txt"
        	(( i++ ))
        done
        ```
        
- **Challenge 7**
    - Consider the [solution](https://drive.google.com/file/d/1jNtGDLNFF0s4NZHzck8PVdHzMOJOjOQh/view?usp=sharing) from the previous challenge. Add a testing condition so that the user enters a positive integer less than 100.
    - Solution
        
        ```bash
        #!/bin/bash
        
        n=0
        while [[ $n -lt 0 || $n -gt 100]]
        do
        	read -p "Please enter a positive integer less than 100: " n
        done
        
        while [[ $i -lt $n ]]
        do
        	touch "$i.txt"
        	(( i++ ))
        done
        ```
        
- **Challenge 8**
    - Consider the [solution](https://drive.google.com/file/d/1A9_Pq0S6RoKZDK73xAC4ZhvkViEgpmp-/view?usp=sharing) from the previous challenge.
    - Change the script so that it creates a file every 3 seconds and the name of each file will be **current_minute_second**
    - Solution
        
        ```bash
        #!/bin/bash
        
        n=0
        while [[ $n -lt 0 || $n -gt 100]]
        do
        	read -p "Please enter a positive integer less than 100: " n
        done
        
        while [[ $i -lt $n ]]
        do
        	minute_second="$(date +%M%S)"
        	echo "Creating $minute_second"
        	touch "$minute_second"
        	(( i++ ))
        	sleep 3
        done
        ```
        
- **Challenge 9**
    - Create a script that takes some IP addresses as arguments and sends one ping packet to each ip address.
    - Solution
        
        ```bash
        #!/bin/bash
        
        for ip in $*
        do
        	ping -c 1 $ip
        	echo "############"
        done
        ```
        
- **Challenge 10**
    - Change the [solution from the previous challenge](https://drive.google.com/file/d/1NpYa22lePLU_m69uv_ga7IP_5KQfoJpG/view?usp=sharing) so that the script reads the IP addresses from [a text file](https://drive.google.com/file/d/1kLZdLi1DB02PuZNoSi2GZZnWnAr0alhP/view?usp=sharing) that is given by the user.
    - Solution
        
        ```bash
        #!/bin/bash
        
        echo -n "Enter the file containing IP addresses: "
        read file
        if [[ -f $file ]]
        then
        	for ip in `cat $file`
        	do
        		ping -c 1 $ip
        		echo "####################"
        	done
        else
        	echo "$file is not a regular file."
        fi
        ```
        
- **Challenge 11**
    - Create a script that takes one or more process names as arguments and checks if each process is running and displays "Process is running!" or "Process IS NOT running!".
    - Check that the script is run with at least one argument.
    - Solution
        
        ```bash
        #!/bin/bash
        
        if [[ $# -lt 1 ]]
        then
        	echo "The script must be run with at least one argument!"
        	exit 10
        fi
        
        process_list="$@"
        
        for process in $process_list
        do
        	output="$(pgrep $process)"
        	if [[ -n $output ]]
        	then
        		echo "Process \"$process\" is running!"
        
        	else
        		echo "Process \"$process\" IS NOT running!"
        	fi
        done
        ```
        
- **Challenge 12**
    - Create a script that takes 3 arguments: a file extension, a source directory, and a destination directory given as absolute or relative paths to the script. The script will copy all the files with that extension from the source directory to the destination directory.
    - Solution
        
        ```bash
        #!/bin/bash
        # $1 - file extention (without .)
        # $2 - source directory
        # $3 - destination directory
        for file in $2/*.$1
        do
        	echo "Copying $file to $3"
        	sleep 1
        	cp $file $3
        done
        ```
        
- **Challenge 13**
    - Create a script that creates both a numerically-indexed array and an associative one.
    - Display the elements of the arrays.
    - Display the indices of the arrays.
    - Append two random elements to the arrays.
    - Solution
        
        ```bash
        #!/bin/bash
        
        declare -a numbers
        numbers=(1 2 3 4 5)
        
        declare -A dictionary
        dictionary[a]="apple"
        dictionary[b]="banana"
        dictionary[c]="kitty kat katto hehe"
        
        echo "${numbers[@]}"
        echo "${!numbers[@]}"
        
        echo
        
        echo "${dictionary[@]}"
        echo "${!dictionary[@]}"
        
        # Appending to the arrays
        numbers+=(6 7)
        echo "Numbers is now ${numbers[@]}"
        
        dictionary+=([d]="doggy dog doggo" [e]="elefante")
        echo "Dictionary is now ${dictionary[@]}"
        ```
        
- **Challenge 14**
    - Declare the following array: **distance=(234 100 26 90 100)**
    - Calculate the sum of the elements of the array.
    - Solution
        
        ```bash
        #!/bin/bash
        distance=(234 100 26 90 100)
        cumsum=0
        for item in "${distance[@]}"
        do
        	let cumsum=cumsum+$item
        done
        
        echo "The sum of distance is $cumsum"
        ```
        
- **Challenge 15**
    - Create a script that reads the lines of a file in an array. Take [this file](https://drive.google.com/file/d/1QTLcya1tQTgddF4OUePQRgLCykN_UPsi/view?usp=sharing) as a sample.
    - The file contains on each line a filename.
    - Test if the file on each line already exists and if not create it.
    - Use arrays to solve this challenge.
    - Solution
        
        ```bash
        #!/bin/bash
        readarray -t files < <(cat files.txt)
        echo "Files to be created: ${files[@]}"
        echo
        for file in "${files[@]}"
        do
        	if [[ -e $file ]]
        	then
        		echo "File $file already exists!"
        	else 
        		echo "Creating $file"
        		touch $file
        	fi
        done
        echo
        echo "Done!"
        ```
        
- **Challenge 16**
    - Change the solution to the previous challenge so that the script will remove the files instead of creating them.
    - Solution
        
        ```bash
        #!/bin/bash
        readarray -t files < <(cat files.txt)
        echo "Files to be removed: ${files[@]}"
        echo
        for file in "${files[@]}"
        do
        	if [[ -e $file ]]
        	then
        		echo "Removing $file"
        		rm $file
        	fi
        done
        echo
        echo "Done!"
        ```
        

---

## Section 07: Functions in Bash

---

- Bash Functions
    - Example (declaring)
        
        ```bash
        #/bin/bash
        funcion log () {
        	echo "I'm a simple log function!"
        }
        
        display(){
        	echo "Hey I'm displaying!"
        }
        
        log 
        display
        ```
        
    - Example (passing in arguments)
        
        ```bash
        #!/bin/bash
        create(){
        	echo "Creating $1"
        	touch $1
        	chmod 400 $1
        	echo "Creating $2"
        	touch $2
        	chmod 644 $2
        }
        create_files aa.txt bb.txt
        ```
        
    - **Bash doesn’t allow you to return values** classically. In fact, if you return a value, that will change the success status of the function
        - For example, doing `return 10` on a function, and then doing `echo #?`, we can see a given function will have a return value of 10
    - However, there is a work around (command substitution)
    - Example
        
        ```bash
        #!/bin/bash
        function lines_in_file(){
        	grep -c "$1" "$2"
        }
        
        # Find usb in /var/log/dmesg
        n=$(lines_in_file "usb" "/var/log/dmesg")
        ```
        
- Variable Scopes in Functions
    - All variables by default **global**
    - Example
        
        ```bash
        #/bin/bash
        var1="AA"
        var2="BB"
        
        function func1(){
        	echo "Inside func1: var1 is $var1 and var2 is $var2"
        	# however, if you do var1="XX", will be recognized everywhere
        	local var1="XX" 
          echo "var1 after making it local: $var1"
        )
        
        func1
        echo "After calling fun1: var1 is $var1 and var2 is $var2"
        ```
        

---

### Challenges

- **Challenge 1**
    - **Create a function called now that prints out the current date and type in the following format: YYYY-MM-DD HH:MM:SS**
    - Call the function.
    - Solution
        
        ```bash
        #!/bin/bash
        
        function now () {
        	echo "$(date +%F\ %H:%M:%S)"
        }
        
        now
        ```
        
- **Challenge 2**
    - **Create a function called rectangle_area that takes 2 arguments: width and height.**
    - The function computes and prints out the area of the rectangle.
    - Call the function with different arguments.
    - Solution
        
        ```bash
        #!/bin/bash
        
        **rectangle_area**(){
        	echo $(($1*$2))
        }
        
        **rectangle_area** 3 5
        **rectangle_area** 10 6
        ```
        
- **Challenge 3**
    - Create a function called **search** that takes a string as an argument.
    - The function will display all the files in the current directory and all its subdirectories that contain the string given as an argument.
    - Solution
        
        ```bash
        #!/bin/bash
        
        function search (){ 
         grep -Ria $1 . | sort 
        }
        
        search while
        ```
        
- **Challenge 4**
    - Create a function called **whereis** that takes 2 arguments: the first argument is a directory and the second argument is a string.
    - Test that the function is called with exactly 2 arguments and that the first one is a directory.
    - The function will recursively search in the directory for all files whose names contain the string.
    - Example: calling **whereis /etc conf** => it will find all files in /etc whose names contain the string conf
    - Solution
        
        ```bash
        #!/bin/bash
        
        function whereis (){
        	if [[ $# -eq 2 ]]
        	then
        		if [[ ! -d $1 ]]
        		then
        			echo "The first argument should be a directory." 
        		else
        			find $1 -name "*$2*";
        		fi
        	else
        		echo "The function should be run with exactly two arguments!" 
        	fi
        }
        
        whereis /etc conf
        ```
        
- **Challenge 5**
    - Create a function called **backup** that takes 2 arguments of the type directory. The first argument is the directory to archive and the second argument is the destination directory (where to create the archive).
    - The archive should be of type **tar.gz** and its name should be **directory-current_date.tar.gz**
    - Check that the function is called with exactly 2 arguments and both are of type directory.
    - Example: **backup /etc $HOME** => it will create an archive of /etc called etc-2022-10.30.tar.gz in the user's home directory.
    - Solution
        
        ```bash
        #!/bin/bash
        function backup(){
        	if [[ $# -eq 2 ]]
        	then
        		if [[ -d $1 && -d $2 ]]
        		then
        			echo "Archiving $1 in $2 ..."
        			# removing leading slash (/) from $1
        			name=${1#/}
        			tar -czf ${2}/${name}-$(date +%F).tar.gz $1 &> /dev/null
        			echo "Done"
        		else
        			echo "Both arguments of the function should be of type directory."
        		fi
        	else
        		echo "The function should be called with exactly 2 arguments."
        	fi
        }
        
        backup /etc $HOME
        ```
        

---

## Appendix: Using the VIM Editor

---

- Keyboard Shortcuts
    - `h` `j` `k` `l` to move around
    - `p` Paste after the cursor
    - `P` Paste before the cursor
    - `x` Delete character under the cursor
    - `r` Replace character under the cursor
    - `R` Replace more than one character
    - `I` (capital i) Insert mode at the beginning of the current line
    - `a` Append text after the cursor
    - `A` Append text to end of current line
    - `o` Write text in a new line below current line
    - `O` (capital o) Write text in a new line above current line
    - `e` **Move forward words!**
    - `dw` Delete until start of the next work, EXCLUDING its first character
    - `d$` Delete after cursor until the end of the line
    - Motions (operator + motion) `d motion`
        - `w` Until the start of the next word, EXCLUDING its first character
        - `e` Until the end of the current work, INCLUDING the last character
        - `$` Until the end of the line, INCLUDING the last character
    - `2w` Move two words forward
    - `3e` Move cursor to the end of the third word forward (inclusive)
    - `0` Go to start of the line
    - `d2w` Delete until start of third word, EXCLUDING its first character
    - `2dd` Delete two lines
    - `u` **Undo last command**
    - `U` Fix a whole line
    - `CTRL-R` Redo
    - `p` to put previously deleted text after the cursor
    - `ce` deletes the whole word and puts you into insert mode
    - `cc` delete the whole line and puts you into insert mode
    - `c$` deletes the rest of the line where your cursor is
    - `CTRL G` to show your location in the file and the file status
    - `G` to move to a line in the file, also to move to bottom of file
    - `gg` to move to start of file
    - `n` ^ To go to next SEARCH result
    - `N` ^^To go back one SEARCH result
    - `CTRL-O` Goes back to where you came from
    - `CTRL-I` ^Go forward
    - `%` Find a matching `)`, `]`, or `}`
    - `*` Find more words like the one under the cursor
    - `#` (Backwards this time) Find more words like the one under the cursor
    - **Write external commands**
        
        `:!ls` just write after the `!`
        
    - Visual Selection
        - `v` Enters visual selection (highlights text)
        - `V` Enters visual selection with lines
        - `Ctrl + v` Enters visual selection with blocks
        - `:'<,'>w` Write new file with selected text
    - **FAST AF**
        - Go into visual mode with `v`
        - Highlight desired text
        - Yank with `y`
        - Move to desired place and paste with `p`
- Commands
    - `:e` Undo what you just did in last line mode (until you last saved the file)
    - `/SEARCH` To search for “SEARCH”
        - `:set ic` *(Ignore case)* Ignore upper/lower case when searching
        - `:set is` *(Ignore search)* Show partial matches for a search phrase
        - `:set hls` (*Hlsearch*) Highlight all matching phrases
        - `:nohlsearch` Remove highlighting of matches
        - `/ignore\c <ENTER>` Ignore case for one search command
        - `:set noic` Switch an option off (ic)
    
    *Use `?` to search in the backward direction instead of `/`*
    
    - `:#,#s/old/new/g`
        
        To change every occurrence of a character string between two lines, where #,# are the line numbers of the range of lines where the substitution is to be done.
        
        - `:%s/old/new/g`
            
            Find every occurrence in the whole file, substitute
            
        - `:%s/old/new/gc`
            
            Find every occurrence in the whole file, with a prompt whether to substitute or not
            
    - `:r FILENAME` Insert the contents of a file
    - `:help (optional command)`
    - Where more features can be enables
        
        Editing the `.vimrc` file
        
    - Command line completion
        1. `:set nocp`
        2. Type start of command (e.g. `:e`)
        3. `CTRL-D` Vim will show a list of commands that start with “e”, keep pressing to go through
        4. Choose desired command with `d<TAB>`
    - `:set nu` Show line numbers
    - `:set nonu` Doesn’t show line numbers
    - `:syntax on` Respective syntax highlighting
    - `:syntax off` Remove syntax highlighting
    - `:100` Move to a line (line 100) quickly
    - `:prev` Access previous file
    - `vim -o a b` Split terminal horizontally with files a and b
    - `vim -d [ORIGINAL] [COPY]` Compare two files (highlights changes/differences)
        
        Also can do `vimdiff`
        

---

## Appendix: Cron, Anacron, and Automation

---

- Task Automation and Scheduling Using Cron (crontab)
    - Edit user cronjob with `cronjob -e`
    - **Syntax**
        - `min hour day month day_of_week command`
        - Hours are given in 24 hr time
        - 0 dow corresponds with Sunday
    - Examples
        - `0 6 * * 0 /root/backup.sh`
        - `0 4,6,10 * * * /root/check_space.sh`
        - `0 9-17 * * 1-5 /root/firewall.sh`
        - `10 4,21 */3  * * /root/task.sh`
            
            Performed every three days
            
        - `@yearly /root/happy_new_year`
        - `@monthly /root/task.sh`
        - `@weekly ...`
        - `@daily ...`
        - `@hourly ...`
        - `@reboot ...`
    - `crontab -r`
        
        Remove crontab file
        
    - [More resources](https://crontab.guru/every-week)
- Scheduling Tasks Using Anacron
    - Run jobs, except better to run anacron here as opposed to cron if machine likely to not be up 24/7

## Appendix: Netfilter and Iptables Firewall

---

- Intro. to Netfilter and Iptables
    - Netfilter is a **software firewall**, a **packet filtering framework inside the Linux kernel**
    - It enables **packet filtering, NAT, PAT, Port forwarding**, and **packet mangling**
- Chain Traversal in a Nutshell
    - **INCOMING TRAFFIC** is filtered on the **INPUT CHAIN** of the **filter table**
    - **OUTGOING TRAFFIC** is filtered on the **OUTPUT CHAIN** of the **filter table**
    - **ROUTED TRAFFIC** is filtered on the **FORWARD CHAIN** of the **filter table**
    - **SNAT/MASQUERADE** is filtered on the **POSTROUTING CHAIN** of the **nat table**
    - **DNAT/Port Forwarding** is filtered on the **PREROUTING CHAIN** of the **nat table**
    - To modify values from the packet’s headers add rules to the **mangle table**
    - To skip the connection tracking add rules with **NOTRACK Target** to the **raw table**
- Iptables Basic Usage
    - Syntax
        
        `iptables [-t table_name] -COMMAND CHAIN_NAME matches: -j TARGET`
        
    
    **Table**
    
    filter (default)
    
    nat
    
    mangle
    
    raw
    
    **Command**
    
    -A (append)
    
    -I (insert)
    
    -D (delete)
    
    -R (replace)
    
    -F (flush)
    
    -Z (zero)
    
    -L (list)
    
    -S (show)
    
    -N 
    
    -X
    
    **CHAIN**
    
    INPUT
    
    OUTPUT
    
    FORWARDING
    
    PREROUTING
    
    POSTROUTING
    
    USER_DEFINED
    
    **matches**
    
    -s source_ip
    
    -d destination
    
    -p protocol
    
    —sport souce_p
    
    —dport dest_p
    
    -i incoming_int
    
    -o outgoing_int
    
    -m mac
    
    -m time
    
    -m quota
    
    -m limit
    
    -m recent
    
    **Target/Jump**
    
    ACCEPT
    
    DROP
    
    REJECT
    
    LOG
    
    SNAT
    
    DNAT
    
    MASQUERADE
    
    LIMIT
    
    RETURN
    
    TEE
    
    TOS
    
    TTL
    
    - `iptables -L`
        
        List contents of firewall
        
    - `iptables -t filter -A INPUT -p icmp --icmp-type echo-request -j DROP`
        
        Drops incoming packets/ping from other source
        
    - `iptables -vnL`
    - `iptables -t filter -A OUTPUT -p tcp --dport 80 -d [www.ubuntu.com](http://www.ubuntu.com) -j DROP`
        
        Deny access from Linux host to specific website
        
- Iptables Options (Flags)
    
    <aside>
    💡 Rules are traversed in order
    
    </aside>
    
    - `-A` : Append the rule to the end of the selected chain
        - nmap mini
            - `nmap -p PORT IP`
                
                Check if a specific `PORT` is open on given `IP`
                
        - `iptables -A INPUT -p tcp --dport 25 -j DROP`
        - `iptables -A INPUT -p tcp --dport 80 -j DROP`
    - `-I` : Insert 1+ rules in the selected chain on a specific position, by default on top (position 1)
        - Example
            - `iptables -I INPUT -p udp --dport 69 -j DROP`
                
                Puts rule in slot 0
                
            - `iptables -vnL`
            - `iptables -I INPUT 3 -p udp --dport 69 -j DROP`
                
                Puts rule in slot 3
                
            
            <aside>
            💡 If you want to undo what you did, add the rule towards the top (will ignore later rules on same port)
            
            </aside>
            
            - `iptables -I INPUT -p tcp --dport 80 -j ACCEPT`
    - `-L` : List all rules in the selected chain. If no chain is selected, all chains are listed
    - `-F` : Flush the selected chain (all chains in the table if none is given)
        
        Equivalent to deleting all the rules one by one
        
        - Example
            - `iptables -F INPUT`
                
                Flushes INPUT table
                
    - `-Z` : Zero the packet and byte counters in all chains, or only the given chain
        - Example
            - `iptables -A OUTPUT -p tcp --dport 22 -j ACCEPT`
            - `ssh IP`
            - `iptables -vnL`
                
                Should see some amount of matched packets
                
            - `iptables -Z`
                
                Will reset packet and bytes counters
                
    - `-N` : Create a new user-defined chain by the given name
        - Example
            - `iptables -N MY_CHAIN`
            - `iptables -vnL`
    - `-X` : Delete the user-defined chain specified
        - Example (deleting the user-defined chain specified above)
            - `iptables -x MY_CHAIN`
    - `-P` : Set the policy for the built-in chain (INPUT, OUTPUT, or FORWARD)
        - Example
            - `iptables -P FORWARD DROP`
    - `-D` : Delete 1+ rules from the selected chain
        
        Specify the line number you want to delete
        
        - `iptables -D OUTPUT 2`
            
            Deletes the second line of the table OUTPUT
            
    - `-R` : Replace a rule in the selected chain
- Where Do We Write Iptables Rules
    
    <aside>
    💡 IPTable Rules written in the terminal are not saved after the system is restarted
    
    </aside>
    
    bloop
    
    ```bash
    #!/bin/bash
    
    # flush all changes so we're not duplicating/appending
    iptables -F 
    # need to also flush nat
    iptables -t nat -F
    
    # dropping incoming ssh traffic
    iptables -A INPUT -p tcp --dport 22 -j DROP
    
    # dropping outgoing http(s) traffic
    iptables -A OUTPUT -p tcp --dport 80 -j DROP
    iptables -A OUTPUT -p tcp --dport 443 -j DROP
    
    # translate private ip addresses to public ip of linux machine/its router
    iptables -t nat -A POSTROUTING -s 10.0.0.0/8 -o enp0s3 -j SNAT --to-source 80.0.0.1
    
    ```
    
- Setting the Default Policy
    - **Policy** specifies what happens to packets that are not matched against any rule
    - **By default Policy is set to accept all traffic**
    - Policy can be changed only for INPUT, OUTPUT, and FORWARD chains
    - Policy can be changed using the `-P` option
        - Check the policy
            
            `iptables -vnL`
            
- Deleting the Firewall
    
    ```bash
    #!/bin/bash
    
    # 1. Set the ACCEPT policy
    iptables -p INPUT ACCEPT
    iptables -p OUTPUT ACCEPT
    iptables -p FORWARD ACCEPT
    
    # 2. Flush all tables
    iptables -t filter -F
    iptables -t nat -F
    iptables -t mangle -F
    iptables -t raw -F
    
    # 3. Delete user-defined chains
    iptables -X
    ```
    
- Filter by IP Address
    
    <aside>
    💡 0/0 - Any IP address with any mask
    
    </aside>
    
    1. Match by Source IP or Network Address
    Match: -s IP, —source IP
        - `iptables -A INPUT -s 100.0.0.0/16 -j DROP`
        - `iptables -A OUTPUT -d 8.0.0.0/8 -j DROP`
            
            Drops any packets destined to any IP addresses that start with 8.
            
    2. Match by Destination IP or Network Address
    Match: -d IP, —destination IP
        - Example
            
            `iptables -A FORWARD -d 80.0.0.1 -j DROP`
            `iptables -A OUTPUT -d [www.ubuntu.com](http://www.ubuntu.com) -j DROP`
            
        - Example (want to block access to www.ubuntu.com)
            - Find IP Addresses
                
                `nslookup www.ubuntu.com`
                
                `dig [www.ubuntu.com](http://www.ubuntu.com)` (Linux only)
                
            
            `iptables -A OUTPUT -d $ip -j DROP`
            
            - Use DNS name
            
            `iptables -A output -d [www.ubuntu.com](http://www.ubuntu.com) -j DROP`
            
            -Makes rules for each of the IP addresses from DNS
            
- Filter by Port
    
    (TCP or UDP port)
    
    1. **Match by a single port**
    Match option: -p tcp —dport port, -p udp —sport port
        - `iptables -A INPUT -p tcp --dport 22 -j DROP`
    2. **Match by multiple ports**
    Match option: -m multiport —sports | —dports port1, port2, …
        - `iptables -A OUTPUT -p tcp -m multiport --dports 80,443 -j ACCEPT`
    
    ```bash
    #!/bin/bash
    
    iptables -F
    
    iptables -A INPUT -p tcp --dport 22 -s IP -j ACCEPT
    # all others are dropped (0/0)
    iptables -A INPUT -p tcp --dport 22 -j DROP
    
    iptables -A INPUT -p tcp --dports 80,443 -j DROP
    ```
    
- Intro. to Stateful Firewalls (Connection Tracking)
    - **Connection Tracking = Stateful Firewall**
        - Connection Tracking = ability to maintain **state information** about connections
        - Stateful firewalls are **more secure** than stateless firewalls
        - Stateful firewalls decide to accept or to drop packets **based on the relations** these packets are with other packets
        - Netfilter is a stateful firewall
        - Packet States
            1. **NEW** - the first packet from the connection
            2. **ESTABLISHED** - packets that are part of an existing connection
            3. **RELATED** - packets that are requesting a new connection and are already part of an existing connection (e.g. FTP)
            4. **INVALID** - packets that are not part of any existing connection
                
                ^Cannot be identified in any state
                
            5. **UNTRACKED** - packets marked within the raw table with the NOTRACK target
            
            Connection tracking can be used even if the protocol itself is stateless (Ex. UDP, ICP)
            
        
        `-m state --state *state*` , where state is a comma-separated values of packet states written in UPPERCASE letters
        
        - Example: `iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT`
            
            ^Only accept packets if they are a part of an existing connection
            
- Implementing Stateful Firewalls (Connection Tracking)
*Creating a stateful firewall from scratch*
    
    ```bash
    #!/bin/bash
    
    iptables -F
    
    iptables -A INPUT -i lo -j ACCEPT
    iptables -A OUTPUT -o lo -j ACCEPT
    
    iptables -A INPUT -p tcp --dport 22 -m state --state NEW -s DESIRED_IP_TO_CONNECT -j ACCEPT
    
    # Drop invalid packets on input and output chains
    # (packets that cannot be identified in any state)
    iptables -A INPUT -m state --state INVALID -j DROP
    iptables -A OUTPUT -m state --state INVALID -j DROP
    # (no services running, no one connects to them from the outside)
    
    iptables -A INPUT -m state --state ESTABLISHED,RELATED -j DROP
    iptables -A OUTPUT -m state --state NEW,ESTABLISHED,RELATED -j DROP
    
    iptables -P INPUT DROP
    iptables -P OUTPUT DROP
    ```
    
- Filter by MAC Address
    - It is possible to filter traffic only by source mac address
    Match: `-m mac --mac-source source_mac_address`
    - Example
        
        `iptables -A INPUT -i wlan0 -m mac --mac-source 08:00:27:55:6f:20 -j DROP`
        
    - Tasks
        1. Drop packets from a specific mac address
        2. Permit only a list of trusted hosts (MACs) through the firewall (NAT router)
    - Example
        
        Hint: use `ifconfig` to find MAC address
        
        `input -A INPUT -m mac --mac-course SOURCE -j -drop`
        
    - Example (nat_filter.sh)
        
        ```bash
        #!/bin/bash
        
        iptables -F FORWARD
        
        PERMITTED_MACS="08:00:27:04:61:21, 07:00:27:04:61:21, 09:00:27:04:61:21, 08:00:27:04:61:56"
        
        for MAC in $PERMITTED_MACS
        do
        	iptables -A FORWARD -m mac --mac-source $MAC -j ACCEPT
        	echo "$MAC permitted"
        done
        
        iptables -P FORWARD DROP
        ```
        
- Match by Date and Time
    - **Syntax:** `-m time *option*`
    - Time match *options*:
        - `--datestart time` : Start and stop time, to be given in ISO 8601
        - `--datestop time` : (YYYY[-MM[-DD[Thh[:mm[:ss]]]]])
        - `--timestart time` : Start and stop daytime (hh:mm[:ss])
        - `--timestop time` : (between 00:00:00 and 23:59:59)
        - `--monthdays value` : List of days on which to match, separated by a comma
        - `!--weekdays value` : List of weekdays on which to match, sep. by a comma
        - `--kerneltz` : Work with the kernel timezone instead of UTC
    - Example
        
        ```bash
        #!/bin/bash
        
        iptables -F
        
        # Accept traffic between 10am-4pm; otherwise, drop
        iptables -A INPUT -p tcp --dport 22 -m time --timestart 10:00 --timestop 16:00 -j ACCEPT
        iptables -A INPUT -p tcp --dport 22 -j DROP
        
        # Accept traffic between 6pm-8am; otherwise, drop
        iptabels -A FORWARD -p tcp --dport 443 -d www.ubuntu.com -m time --timestart 18:00 --timestop 8:00 -j ACCEPT
        iptabels -A FORWARD -p tcp --dport 443 -d www.ubuntu.com -j DROP
        
        ```
        
- The ACCEPT and DROP Targets
    - Both `ACCEPT` and `DROP` are terminating targets
    - `DROP` denies the packet and doesn’t send any packet back to the source
    - How ping works
        
        ![Screenshot 2022-12-15 at 8.21.49 PM.png](Shell%20Scripting%20Notes%20c0fb892ba5894b6d995dfd2a77307d15/Screenshot_2022-12-15_at_8.21.49_PM.png)
        
    - Example
        
        ```bash
        #!/bin/bash
        
        iptables -A INPUT -p icmp --icmp-type echo-request -s HOST-IP -j ACCEPT
        iptables -A INPUT -p icmp --icmp-type echo-request -j DROP
        ```
        
- The LOG Target
    - `LOG` is a non-terminating target
    - It logs detailed information about packet headers
    - Logs can be read with `dmesg` or from `syslogd` daemon
    - `LOG` is used instead of `DROP` in the debugging phase
    - `ULOG` has MySQL support (extensive logging)
    - Options
        
        `--log-prefix`
        
        `--log-level`
        
    - Example
        
        `iptables -A INPUT -p tcp --dport 22 -syn -j LOG --log-prefix="incoming ssh:" --log-level info`
        
    - Example
        
        ```bash
        #!/bin/bash
        
        iptables -A INPUT -p tcp --dport 22 --syn -j LOG --log-prefix="incoming ssh traffic:" --log-level info
        iptables -A INPUT -p tcp --dport 22 -j DROP
        dmesg
        tail -f /var/log/kern.log
        ```