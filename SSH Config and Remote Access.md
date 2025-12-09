## SSH Configuration and Remote Access 

**SSH Configuration Files**

- Server Configuration (sshd_config)
  - **Location:** `/etc/ssh/sshd_config`
    - **Purpose:** Configures the SSH daemon (server-side)
    - **Common settings:** Port number, authentication methods, access controls
    - **Default port:** 22 (can be changed for security)

- Client Configuration
  - **System-wide:** `/etc/ssh/ssh_config`
  - **User-specific:** `~/.ssh/config`


- Example Per User Client Configuration
```bash
Host server1
    Hostname server1vmguests.com
    Port 1022
    User grant
    IdentityFile ~/.ssh/server1.key
```

> Usage: Instead of `ssh -p 1022 grant@server1vmguests.com -i ~/.ssh/server1.key`, you can simply use:  `ssh server1`

**SSH Key-Based Authentication**

1. Generate SSH Key Pair
```bash
# Modern recommended approach (Ed25519)
ssh-keygen -t ed25519 -C "your_email@example.com"

# Traditional RSA (for older systems)
ssh-keygen -t rsa -b 4096
```


2. Key Files Created
  - **Private key:** `~/.ssh/id_ed25519` (or `id_rsa`) - **Keep secure, never share**
  - **Public key:** `~/.ssh/id_ed25519.pub` (or `id_rsa.pub`) - Safe to share

3. Copy Public Key to Remote Server
```bash
ssh-copy-id user@remote_host
```

**Important:** `ssh-copy-id` automatically sets correct permissions: 
- `~/.ssh/` directory:  **700** (drwx------)
- `~/.ssh/authorized_keys`: **600** (-rw-------)


4. Verify Setup
```bash
# View your public key
cat ~/.ssh/id_ed25519.pub

# View authorized keys on remote server
cat ~/.ssh/authorized_keys

# Check known hosts
cat ~/.ssh/known_hosts
```

5. Add to SSH Agent (Optional)
```bash
ssh-add ~/.ssh/id_ed25519
```

---

- Secure File Transfer Methods

1. SCP (Secure Copy)
```bash
# Local to remote
scp /local/path/file user@remote_host:/remote/path/

# Remote to local
scp user@remote_host:/remote/path/file /local/path/

# Copy entire directory
scp -r /local/directory user@remote_host:/remote/path/
```

2. Rsync
```bash
# Sync local to remote with archive mode
rsync -av /local/directory/ user@remote_host:/remote/directory/

# Additional useful options
rsync -avHAX /local/directory/ user@remote_host:/remote/directory/
```

**Options explained:**
- `-a`: Archive mode (recursive, preserves permissions, ownership, symlinks)
- `-v`: Verbose output
- `-H`: Preserve hard links
- `-A`: Preserve ACLs
- `-X`: Preserve extended attributes

3. SSH Tunnels for Data Streaming
```bash
# Stream file through SSH
cat file | ssh user@remote_host "cat > /path/to/destination/file"

# Bit-by-bit copy with dd
dd if=/dev/sda | ssh user@remote_host "dd of=/backup/disk.img"
```

4. SFTP (SSH File Transfer Protocol)
```bash
# Interactive SFTP session
sftp user@remote_host

# Common SFTP commands within a session
# put local_file remote_path
# get remote_file local_path
# ls, cd, mkdir, etc.
```

---

- Remote Access from Windows

- Using PuTTY for SSH
  1. **Download:** Visit [putty.org](https://putty.org) and download PuTTY
  2. **Configuration:**
   - Enter Linux host IP address
   - Ensure connection type is set to SSH
   - Port 22 (or custom port if changed)
   - Optional: Save session for reuse
  3. **Connect:** Click "Open" and enter credentials

- Using WinSCP for File Transfer
  1. **Download:** Visit [winscp.net](https://winscp.net) 
  2. **Installation:** Run installer with typical setup
  3. **Configuration:**
     - Import PuTTY sessions automatically
     - Use same credentials as SSH connection
  4. **File Transfer:** Drag and drop between local (left) and remote (right) panes


- Remote Access from macOS/Linux

- Basic SSH Connection
```bash
# Get IP address of target Linux host
ip addr show

# or on older systems
ifconfig

# Connect from macOS/Linux
ssh username@ip_address

# Logout
exit
```


- Remote Access from Mobile Devices

- iOS: Server Auditor (or alternatives)
1. **Install:** Download "Termius" or "Server Auditor" from App Store
2. **Configure Host:**
   - Alias:  Descriptive name for your server
   - Username: SSH username
   - Hostname: IP address of Linux machine
   - Port: 22 (or custom port)
   - Password: SSH password or configure key-based auth
3. **Connect:** Tap configured host to connect

- Android: Termux or ConnectBot
  1. **Install:** Download "Termux" (full terminal) or "ConnectBot" from Google Play
  2. **For ConnectBot:**
     - Enter:  `username@ip_address`
     - Confirm fingerprint when prompted
     - Enter password
  3. **For Termux:**
   ```bash
   pkg install openssh
   ssh username@ip_address
   ```

---

- Security Best Practices

- SSH Server Hardening
```bash
# Edit /etc/ssh/sshd_config
sudo nano /etc/ssh/sshd_config

# Recommended security settings: 
Port 2222                    # Change from default 22
PermitRootLogin no          # Disable root login
PasswordAuthentication no   # Use keys only (after setup)
PubkeyAuthentication yes    # Enable key-based auth
MaxAuthTries 3             # Limit login attempts
```

-Key Management
  - Use strong passphrases for private keys
  - Regularly rotate SSH keys
  - Remove old/unused public keys from `authorized_keys`
  - Use SSH agent for convenience without compromising security

- File Permissions (Critical)
```bash
# Correct SSH directory permissions
chmod 700 ~/. ssh
chmod 600 ~/.ssh/id_*
chmod 644 ~/.ssh/id_*. pub
chmod 600 ~/.ssh/authorized_keys
chmod 644 ~/.ssh/known_hosts
```


This guide now provides accurate, current best practices for SSH configuration and remote access across all major platforms. 
