## Shell Session Initialization & Cleanup Scripts

Shell scripts can run automatically when you start or end a shell session, allowing you to customize your environment, set variables, and perform cleanup tasks.

The behavior of these scripts depends heavily on whether the shell is a login shell or an interactive non login shell:

  - Login Shell: Started with authentication (e.g., console login, ssh, su -l or su -).
  - Interactive Non-Login Shell: Started without full authentication (e.g., opening a new terminal tab/window in a desktop environment, or running bash from an existing shell).

---

**Scripts Executed at Session Start: Login Shells**
  - These scripts run when a new login shell starts.
    
  - User Specific Files (for bash)
    - `~/.bash_profile`: The primary user specific login script for bash.
    - `~/.profile`: A fallback login script reads if `~/.bash_profile` doesn't exist. It's also the standard login script for other shells (like sh, dash).

  - Example: `vim ~/.bash_profile or vim ~/.bashrc`
```
#!/bin/bash
# ~/.bash_profile or ~/.bash_login

# Personal welcome message
echo "Welcome back, $(whoami)!"

# Check for mail
if [ -f /var/mail/$(whoami) ]; then
    echo "You have mail!"
fi

# Display last login
last_log=$(last -n 1 $USER | head -n 1)
if [ ! -z "$last_log" ]; then
    echo "Last login: $last_log"
fi

# Set personal environment
export MY_WORK_DIR="$HOME/projects"
export PS1="\u@\h:\w\$ "
```
<img width="854" height="228" alt="Screenshot 2025-12-07 at 7 33 21 AM" src="https://github.com/user-attachments/assets/41b224c6-1f2c-4458-aabe-75e121f20f46" />


  - System Wide Files
    - `/etc/profile`: The primary system-wide login script. Runs for all users during login shell initialization.
    - Files in `/etc/profile.d/`: A directory containing supplemental scripts that are sourced by `/etc/profile`.
      > This is the recommended place for system administrators to add global customizations without editing `/etc/profile` directly.
    - Example:
`sudo vim /etc/profile`
  ```
#!/bin/bash
# /etc/profile or /etc/profile.d/custom.sh

# Display system information
echo "Welcome to $(hostname)"
echo "System uptime: $(uptime -p)"
echo "Current date: $(date)"

# Set environment variables
export EDITOR=vim
export HISTSIZE=5000

# Custom PATH additions
export PATH=$PATH:/usr/local/custom/bin
```
<img width="799" height="155" alt="Screenshot 2025-12-07 at 7 22 15 AM" src="https://github.com/user-attachments/assets/f456fef4-8098-4bf2-88e0-66b759ddacfc" />

---

**Interactive Non Login Shells**
  - These scripts run when an interactive shell starts that is not a login shell.
    
  - User Specific File
    - `~/.bashrc`: This is the most common file for user customizations. It typically sets aliases, shell functions, and prompt (PS1) definitions that are desired in every interactive shell.
      ```
      # Example ~/.bashrc additions

      alias ll='ls -la'                   # Create shortcut: 'll' lists all files in long format
      alias gpull='git pull origin main'  # Shortcut for pulling the latest changes from main branch
      export PS1='[\u@\h \W]\$ '          # Customize shell prompt: user@host and current directory
      ```
    
  - System Wide File
    - `/etc/bash.bashrc` (or `/etc/bashrc` on some systems): The global counterpart to `~/.bashrc`.

---

**Scripts Executed at Session End: .bash_logout**

**User Specific Logout Script**
  - `~/.bash_logout`: Executes when a login shell exits gracefully (e.g., by typing `exit`, `logout`, or pressing Ctrl+D).
  > Limitation: This file only runs for login shells. It will not execute when closing a typical graphical terminal emulator window, as those usually start non login shells.
  - Uses:
    - Clear the terminal screen for privacy.
    - Delete temporary files created during the session.
    - Log session statistics or resource usage.
  - Example:
    - `~/.bash_logout`
    ```
    #!/bin/bash                          # Script interpreter
    clear                                 # Clear the screen

    if [ -d "$HOME/.session_cache" ]; then  # Check if cache directory exists
    rm -rf "$HOME/.session_cache/"*     # Safely remove its contents
    fi                                     # End of directory check

    echo "Session ended at $(date). Goodbye, $USER!"   # Print farewell message
    ```
    
**System Wide Logout**

There is no standard system wide logout script equivalent to `/etc/profile`. Logout actions are typically handled per user or by the system's session manager in graphical environments.

---

**Execution Order:**
  1. /etc/profile
  2. /etc/profile.d/*.sh
  3. ~/.bash_profile or ~/.bash_login (whichever exists first)
  4. ~/.profile (if bash specific files don't exist)
  5. ~/.bashrc (if called from login scripts)
  6. ~/.bash_logout (on logout)

---
