# Compression and Archiving

In Linux, file compression reduces file size by encoding data more efficiently. This is particularly useful for text files, which can often be reduced in size by 50-75%. However, multimedia files (images, videos, audio) are typically already compressed and may see less reduction (sometimes only 10-25%).
  - Linux treats nearly everything as a file, making compression tools universally applicable
  - Compression can significantly reduce storage requirements and transfer times
  - Different tools offer varying compression ratios and speeds

---
**Compression Tools**
---

**1. gzip (GNU Zip)**
  - General purpose compression with a good balance of speed and ratio
  - Algorithm: LZ77 (DEFLATE)
    - LZ77 reduces data size by replacing repeated sequences with pointers to their earlier occurrences within a fixed size buffer (sliding window). 

- Compression:
```bash
gzip filename           # Compresses file, creates filename.gz, removes original
gzip -v filename        # Verbose mode (shows compression details)
```
- Decompression:
```bash
gunzip filename.gz      # Decompresses, removes .gz extension
gzip -d filename.gz     # Alternative method
```


| **Option**  | **Description**                                                                                  | **Example**                                |
|-------------|--------------------------------------------------------------------------------------------------|--------------------------------------------|
| `-c`        | Write to standard output instead of creating a file.                                             | `gzip -c file > file.gz`                   |
| `-d`        | Explicitly decompress (default for `gunzip`).                                                   | `gzip -d file.gz`                          |
| `-k`        | Keep the original file during compression or decompression.                                     | `gzip -k file`                             |
| `-l`        | Show compression statistics such as name, original size, compressed size, and compression ratio.| `gzip -l file.gz`                          |
| `-r`        | Recursively compress/decompress files in directories and their subdirectories.                  | `gzip -r mydir`                            |
| `-v`        | Display verbose output, showing what’s being compressed/decompressed.                           | `gzip -v file`                             |
| `-q`        | Suppress warnings and messages during the operation.                                            | `gzip -q file.gz`                          |
| `--fast`    | Optimize for faster compression (lower compression ratio).                                      | `gzip --fast file`                         |
| `--best`    | Optimize for best compression ratio (default behavior).                                         | `gzip --best file`                         |
| `--stdout`  | Alias of `-c`. Write output to standard output.                                                 | `gunzip --stdout file.gz > decompressed`   |

Example:
```
cp /etc/hosts /usr/share/dict/words ~/    # Copy 'hosts' and 'word' to home directory
ls -lh hosts words 

gzip -v words hosts
ls -lh hosts.gz words.gz 

gunzip hosts.gz words.gz 
ls -lh hosts words 
```
<img width="766" height="149" alt="Screenshot 2025-12-08 at 9 07 23 AM" src="https://github.com/user-attachments/assets/b5ae76d1-42ff-4cbf-99bc-f2879a99558e" />

<img width="764" height="302" alt="Screenshot 2025-12-08 at 9 08 16 AM" src="https://github.com/user-attachments/assets/217a3fdd-73d4-4089-9231-93c68d70b391" />

---

**2. bzip2**
  - Better compression ratios than gzip (slower compression)
  - Algorithm: Burrows Wheeler Transform with Huffman coding
    - BWT clusters similar characters, and Huffman Coding compresses them efficiently using frequency based variable length encoding.

- Compression:
```bash
bzip2 filename          # Compresses file, creates filename.bz2, removes original
bzip2 -v filename       # Verbose mode
```

- Decompression:
```bash
bunzip2 filename.bz2    # Decompresses, removes .bz2 extension
bzip2 -d filename.bz2   # Alternative method
```

| **Option**  | **Description**                                                                                  | **Example**                                 |
|-------------|--------------------------------------------------------------------------------------------------|---------------------------------------------|
| `-k`        | Keep the original file during compression or decompression.                                     | `bzip2 -k file`                             |
| `-d`        | Explicitly decompress the file (default for `bunzip2`).                                         | `bzip2 -d file.bz2`                         |
| `-z`        | Force compression (default for `bzip2`).                                                        | `bzip2 -z file`                             |
| `-c`        | Write to standard output instead of creating a file.                                            | `bzip2 -c file > file.bz2`                  |
| `-f`        | Force overwrite of existing files during compression or decompression.                          | `bzip2 -f file`                             |
| `-v`        | Display verbose output, showing compression/decompression progress.                             | `bzip2 -v file`                             |
| `-q`        | Suppress warnings and messages during the operation.                                            | `bzip2 -q file.bz2`                         |
| `--fast`    | Optimize for faster compression (less compression ratio).                                       | `bzip2 --fast file`                         |
| `--best`    | Optimize for the best compression ratio (default behavior).                                     | `bzip2 --best file`                         |

```
cp /etc/hosts /usr/share/dict/words ~/        # Copy 'hosts' and 'words' to the home directory


ls -lh ~/hosts ~/words                        # Verify the copied files


bzip2 -v -k ~/hosts ~/words                    # Compress both files using 'bzip2' and keep originals


ls -lh ~/hosts ~/hosts.bz2 ~/words ~/words.bz2    # Verify the compressed files (*.bz2) and original files


bunzip2 -v -k ~/hosts.bz2 ~/words.bz2          # Decompress the compressed files while keeping the .bz2 versions


ls -lh ~/hosts ~/hosts.bz2 ~/words ~/words.bz2    # Verify the decompressed files are restored along with the compressed versions
```

---

**3. zip/unzip**
  - The zip format is cross platform, making it ideal for sharing files between Linux and Windows systems.
  - Algorithm:
    - The ZIP format primarily uses the Deflate algorithm, a combination of LZ77 (Lempel-Ziv) string matching and Huffman coding. 

- Compression:
```bash
zip [options] compress_name.zip file1 file2 ...
zip [options] compress_name.zip directory/
```
  - Option
    - -r : Recursively add files or directories.
    - -v : Verbose mode, show details during compression.
    - -q : Quiet mode, suppress output.
    - -9 : Maximum compression (default).
    - -1 : Minimum compression for faster speed.
    - -y : Store symbolic links as symbolic links, not the files they point to.
    - -u : Update files in an existing ZIP file.
      
- Decompression:
```bash
unzip [options] compress_name.zip
```
  - Option
    - -l : List the contents of the archive without extracting.
    - -v : Display detailed information about the archive.
    - -d directory : Extract the archive into the specified directory.
    - -o : Overwrite existing files without prompting.
    - -q : Suppress output during extraction.
    - -n : Never overwrite existing files when extracting.

**Example:**
  - Compression
```
# Copy files as examples for compression
cp /etc/hosts /etc/resolv.conf ~/

# Compress multiple files into a ZIP archive
zip my_files.zip ~/hosts ~/resolv.conf

# Compress a directory
zip -r my_backup.zip /etc/

# Verify the compressed files
ls -lh my_files.zip my_backup.zip
```
  - Decompression
```
# Decompress the ZIP archive into the current directory
unzip my_files.zip

# List the contents of the ZIP file
unzip -l my_backup.zip

# Decompress to a specific directory
unzip -d ~/restored my_backup.zip
```

---

**More examples:**

1. Compress and Replace the Original

This approach removes the original file and replaces it with its compressed version.

Example Using `gzip`:

```bash
# Copy example file to current directory
cp /usr/share/dict/words .

# Compress the file (replaces the original with compressed version)
gzip -v words

#Verify: You will see 'words.gz', and the original file 'words' is deleted
ls -lh
```

Predicted Output:
```plaintext
words:     4.0M -> 1.2M (30.0% saved)
# After running `ls`, you'll see:
-rw-r--r--  1 user user 1.2M Dec 08  words.gz
```

Decompress:
```bash
gunzip words.gz

# Verify: The original file 'words' is restored, and 'words.gz' is deleted
ls -lh
```


2. Create Compressed Copy (Preserve Original)
   
- Example 1: Using `gzip`:

```bash
# Copy file to current directory and verify
cp /etc/kdump.conf .
ls -lh kdump.conf

# Compress and create a copy, preserving the original
gzip -vc kdump.conf > kdump.conf.gz

# Verify: Both the original file and the compressed copy are present
ls -lh
```

- Example 2: Using `bzip2`:

```bash
# Compress and preserve the original file
bzip2 -vc kdump.conf > kdump.conf.bz2

# Verify: Original file remains, and a .bz2 compressed file is created
ls -lh
```

- Example 3: Using `zip`:

```bash
# Compress while automatically preserving the original (zip always keeps the original by default)
zip kdump.conf.zip kdump.conf

# Verify: Original file remains, and a .zip compressed file is created
ls -lh
```

---

**Archiving**

  - Archiving combines multiple files/directories into a single file without compression (by default).
  - Compression can then be applied separately or simultaneously.
  - Archiving combines multiple files and directories into a single file for easier storage, transfer,
and backup.

| **Tool** | **Purpose**                        | **Key Features**                           |
| -------- | ---------------------------------- | ------------------------------------------ |
| **tar**  | Tape Archive (most common)         | Preserves permissions, handles directories |
| **zip**  | Cross-platform archives            | Windows/macOS compatible                   |
| **7z**   | High compression ratio             | Multiple compression algorithms            |
| **cpio** | Less common, used in RPM/initramfs | Compatible with legacy UNIX workflows      |

**tar**
```
tar [options] [archive-file] [files-to-archive]
```

- tar Command Options
  - `-c` : Create a new archive
  - `-v` : Verbose (show files being processed)
  - `-f` : Specify archive filename **(must be followed by filename)**
  - `-t` : List contents of archive
  - `-r` : Append files to existing archive
  - `-x` : Extract files from archive
  - `-z` : Filter archive through gzip (use .tar.gz or .tgz extension)
  - `-j` : Filter archive through bzip2 (use .tar.bz2 or .tbz extension)
  - `-J` : Use xz compression
  - `-C` : Extract to specific directory
  - `-p` : Preserve permissions
  - `--exclude` : Exclude files
  - `-p` : Preserve permissions


- Creating Archives

```bash
# Create uncompressed archive
tar -cvf archive.tar file1 file2 directory/

# Create compressed archives
tar -zcvf archive.tar.gz files/                     # gzip compression
tar -jcvf archive.tar.bz2 files/                    # bzip2 compression
tar -cvf archive.tar files/ | gzip > archive.tgz    # Alternative syntax
```

- Example

```bash
# Create backup directory and copy configuration files
mkdir -p ~/backup
sudo cp /etc/*.conf ~/backup/

# Create archive
sudo tar -cvf backup.tar /etc/*.conf /var/log ~

# List archive contents
tar -tvf backup.tar

# Add more files to existing archive (uncompressed only)
sudo tar -rvf backup.tar /etc/passwd /etc/hosts
```

**Compressed Archives**

Archives are often large, so compressing them saves a significant amount of space. There are two approaches:

  - Method 1: Archive Then Compress

```bash
# Create plain archive
sudo tar -cvf backup1.tar /etc/*.conf /var/log

# Compress the archive
gzip -v backup1.tar           # Creates backup1.tar.gz

# Verify size reduction
ls -lh backup1.tar.gz
```

  - Method 2: Archive and Compress Simultaneously

```bash
# Using gzip compression
sudo tar -zcvf newimpfile.tar.gz /etc/*.conf /var/log
sudo tar -zcvf newimpfile.tgz /etc/*.conf /var/log              # .tgz is an alternative extension

# Using bzip2 compression
sudo tar -jcvf newimpfile1.tar.bz2 /etc/*.conf /var/log
sudo tar -jcvf newimpfile1.tbz /etc/*.conf /var/log              # .tbz is an alternative extension
```

---

**Limitations**

  - Cannot Update Compressed Archives
```bash
# This will FAIL:
tar -rvf newimpfile.tar.gz /etc/passwd
# Error: "Cannot update compressed archives."
```

  - Solution: Decompress, update, then recompress
```
gunzip newimpfile.tar.gz                # 1. Decompress
tar -rvf newimpfile.tar /etc/passwd     # 2. Update archive
gzip newimpfile.tar                     # 3. Recompress
```

**Extracting Archives: Extract to Specific Directory**

```bash
# Create extraction directory
mkdir extractdir
cd extractdir

# Extract uncompressed tar archive
tar -xvf ~/newimpfile.tar

# Extract gzip-compressed tar archive
tar -zxvf ~/newimpfile.tar.gz

# Extract bzip2-compressed tar archive  
tar -jxvf ~/newimpfile.tar.bz2

# Alternative: Extract to a specific directory without cd
tar -zxvf ~/newimpfile.tar.gz -C /path/to/target/directory
```
---

> Note: The "Everything is a file" philosophy in Linux means these tools can work on various file types, but actual compression effectiveness depends on the content. Text and source code compress well, while already-compressed files (JPEG, MP4, etc.) see minimal reduction.
