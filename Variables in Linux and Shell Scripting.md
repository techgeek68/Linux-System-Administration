## Variables in Linux/Shell Scripting

---

- A variable is a named storage location that holds a value. This value can be a string of text, a number, or other data, and it can be referenced and manipulated throughout the shell session or within a script.

- Syntax:
```bash
VARIABLE_NAME=<value>
```
  - Variable Naming Rules
    - Can contain letters (a-z, A-Z), digits (0-9), and underscores (_)
    - Must start with a letter or underscore
    - Case sensitive
    - No spaces around the equals sign
    - Avoid special characters: !, @, #, $, %, *, etc.
      - No quotes var=value
        - Only for single words, no spaces/specials, Never use with spaces or $, *, etc.
      - Double quotes var="value"
        - Use by default, Allows variable expansion: $var, $(cmd), Prevents word splitting
      - Single quotes var='value'
        - Everything literal, No expansion at all, Use for regex, SQL, paths with special chars

 - Calling Variables
   - Accessing Variables
     ```
     # Use $ before variable name
     echo $name
     echo "Hello, $name!"

     # Alternative syntax for clarity
     echo ${name}
     ```

Example:
<img width="779" height="207" alt="Screenshot 2025-12-07 at 6 47 00 AM" src="https://github.com/user-attachments/assets/da234ef1-55c0-4b89-ba2c-a5fed9454816" />

   - set Command
     - Displays all shell variables and functions
     - Output can be extensive (pipe to grep for filtering)
     - Includes both user defined and system variables
     - Example:`set | grep -E '^USER|^PATH|^HOME'`
       
   - env Command
     - Displays only exported environment variables
     - Cleaner output than set
     - Used to run programs with modified environments: `env VAR1=value1 VAR2=value2 command`
     - Example to see all environment variables: `env | sort`
       
   - printenv Command
     - Alternative to env for viewing environment variables
     - Can display specific variable: `printenv HOME`
       - Returns exit code 0 if variable exists, 1 otherwise
         
---

- Types of Variables
  - Local Variables (Shell Variables)
    - Only available in the current shell session and lost when the shell session ends.
    - Not inherited by child processes
    - Created without any special keywords and conventionally named in lowercase.
    - Examples
      ```
      # Define a local variable
      my_var="Hello World!"
      count=42
      ```
      
  - Environment Variables (System Defined)
    - Available to the current shell and all child processes
    - Inherited by programs and scripts launched from the shell
    - Usually defined in uppercase by convention
    - Examples:
      ```
      # Create an environment variable
      export MY_VAR="global value available for child process!"

      or

    
      MY_VAR="value"
      export MY_VAR
      ```
      
---

- Variable Types & Declaration
  - Regular Variables
      ```
      # Local to current shell
      my_var="hello"

      # Make available to subprocesses
      export MY_VAR="global"
      ```
  - Read only Variables
      ```
      readonly PI=3.14159
      PI=3.14  # Error: readonly variable
      ```
  - Integer Variables
      ```
      declare -i count=10
      count=count+5  # Can do arithmetic without $((...))
      ```
  - Array Variables
      ```
      # Indexed arrays
      declare -a fruits=("apple" "banana" "cherry")
      echo ${fruits[0]}  # apple

      # Associative arrays (bash 4+)
      declare -A colors=([red]="#FF0000" [green]="#00FF00")
      echo ${colors[red]}  # #FF0000
      ```
  - String Variables
      ```
      declare -r greeting="Hello World"  # Readonly string
      declare -l lower_string="HELLO"    # Automatically lowercase
      declare -u upper_string="hello"    # Automatically uppercase
      ```
      
- Command Substitution in Variables
  ```
  # Store command output in a variable
  current_date=$(date)
  file_count=`ls | wc -l`  # Older syntax, less preferred

  # Store command exit status
  mkdir /tmp/test
  status=$?
  ```
  
- Special Shell Variables
  - Predefined by the shell, provide system and script information.
    - Here is the table in Markdown format:

| Variable        | Description                      |
| --------------- | -------------------------------- |
| `$0`            | Script/command name              |
| `$1`, `$2`, ... | Positional parameters            |
| `$#`            | Number of arguments              |
| `$@`            | All arguments as separate words  |
| `$*`            | All arguments as a single string |
| `$?`            | Exit status of last command      |
| `$$`            | Current shell's PID              |
| `$!`            | PID of last background job       |
| `$USER`         | Current username                 |
| `$HOME`         | User's home directory            |
| `$PWD`          | Current working directory        |
| `$PATH`         | Command search path              |

  - echo $0
  - echo $PATH
  - echo $HOME

---

- Unsetting Variables
     ```
     unset variable_name      # Remove variable
     unset -v var1 var2 var3  # Remove multiple variables
     ```

---
---

**The PATH Variable**

`PATH` is a critical environment variable that specifies an ordered list of directories where the shell looks for executable commands. It allows you to run commands without typing their full absolute path.

- Commands Path:
  - Administrative Commands:
    - Typically stored in `/usr/sbin` (e.g., `useradd`, `userdel`). Usually requires privilege escalation.
  - Non Administrative Commands:
    - Typically stored in `/usr/bin` (e.g., `ls`, `mkdir`, `vi`). Available to all users.
```bash
which mkdir    # Output: /usr/bin/mkdir
which useradd  # Output: /usr/sbin/useradd
```

- Viewing and Understanding PATH
```bash
echo $PATH
```
Output example: `/home/user/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin`
  - Colons separate directories (`:`).
  - The shell searches them from left to right.


**Adding a Directory to PATH**

- Scenario:
  - You have a script `helloworld.sh` in `~/scripts` that you want to run from anywhere.

Solution 1: Use the Absolute Path
```bash
~/scripts/helloworld.sh
```

Solution 2: Add the Directory to PATH

A. Temporary Addition (Lasts only for current session)
>Note: Never set `PATH` to a single directory, as it will erase all existing search paths, breaking most commands.
```bash
# WRONG: PATH=~/scripts
# CORRECT: Append your directory to the existing PATH
PATH=$PATH:~/scripts
```
>This change is lost when you close the terminal.

Example:
<img width="802" height="434" alt="Screenshot 2025-12-07 at 6 35 38 AM" src="https://github.com/user-attachments/assets/228ce85e-4d44-4a70-aff5-303fe06f2ca2" />


B. Permanent Addition

For a Current User
  - Edit the user's shell configuration file in their home directory.
    - `~/.bash_profile`: Executed for **login shells**.
    - `~/.bashrc`: Executed for **interactive non login shells** (the most common for terminal use).
```
vi ~/.bashrc
```
```bash
# Add at the end:
export PATH="$PATH:~/scripts"

# Save and exit (:wq)
```

Activate the change:
  1. Log out and log back in, OR
  2. Run `source ~/.bashrc` or `. ~/.bashrc`

Example:

<img width="800" height="64" alt="Screenshot 2025-12-07 at 6 38 19 AM" src="https://github.com/user-attachments/assets/cefe2056-7425-4b38-ba8d-efc861ca4f84" />

<img width="799" height="241" alt="Screenshot 2025-12-07 at 6 39 25 AM" src="https://github.com/user-attachments/assets/6a6223c5-974a-4a8e-8dfb-116970da5884" />

System Wide (All Users)
  - Edit the global configuration file (requires root privileges).
    - `/etc/profile`: System wide login shell configuration.
    - `/etc/bash.bashrc` or `/etc/bashrc`: System-wide configuration for interactive shells (distribution dependent).
```bash
sudo vi /etc/profile
# Add the export line (as above) at an appropriate location.
# Save, exit, and source the file or log out/in.
```

After setting PATH:
```bash
helloworld.sh  # Should now work from any directory
which helloworld.sh # Should show its full path
```

---

**Examples:**

- 1. Calculating Area: Temporary Variables in a Session
```bash
length=20
breadth=10
echo "Length is $length, Breadth is $breadth"
Area=$((length * breadth))
echo "Area is: $Area"
# Or in one line:
echo "Area is: $((length * breadth))"
```

<img width="800" height="168" alt="Screenshot 2025-12-07 at 6 31 12 AM" src="https://github.com/user-attachments/assets/bc6628e3-3d45-4615-b194-dcc4a2ef7692" />


- 2. Print your full name.
```bash
read -p "Enter your first name: " fname
read -p "Enter your last name: " lname

fullname="$fname $lname"
echo "Your full name is: $fullname"

```

---

**- Understanding Quotes in Shell**

1. Double Quotes (`" "`)
  - Interpret most text literally.
  - Allow variable expansion (`$VAR`) and command substitution (`` `command` `` or `$(command)`).
  - The backslash (`\`) can be used to escape special characters (`$`, `` ` ``, `"`, `\`).
  - Examples:
```bash
echo "My home is: $HOME"      # Variable is expanded.
echo "Cost is: \$5"           # Escapes the `$`, prints literally.
echo "Time is: `date`"        # Command substitution works.
```

2. Single Quotes (`' '`)
  - Interpret everything literally. No variable or command substitution occurs.
```bash
echo 'Path is: $HOME'  # Prints: Path is: $HOME
```

3. Backticks (`` ` ` ``)
  - Used for command substitution. Executes the enclosed command and inserts its output.
  - Largely superseded by the clearer `$(command)` syntax.
```bash
echo "Today is `date`"      # Old style
echo "Today is $(date)"     # Preferred modern style
```

<img width="799" height="344" alt="Screenshot 2025-12-07 at 6 28 09 AM" src="https://github.com/user-attachments/assets/cde36b98-b38b-4ee8-bf95-cb17e4cf77bb" />

---
