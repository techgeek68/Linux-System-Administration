# String Manipulation Tools in Linux/Unix

String manipulation tools are a set of command-line utilities that allow you to perform various operations on text data, such as viewing, searching, filtering, transforming, and processing text files.

## Viewing File Contents

### `cat` - Concatenate and Display Files
The `cat` command displays the entire content of a file. It is best suited for small files.

**Syntax:**
```bash
cat <filename>
```

**Example:**
```bash
cat /etc/hosts
```

### `less` - View Long Files Interactively
The `less` command displays file content one screen at a time, allowing both forward and backward navigation. It's ideal for viewing long files.

**Syntax:**
```bash
less <filename>
```

**Example:**
```bash
less /etc/passwd
```

#### Navigation in `less`:
- **Forward search:** `/text` then press Enter
- **Reverse search:** `?text` then press Enter
- **Next match:** `n`
- **Previous match:** `N`
- **Go to end:** `G`
- **Go to beginning:** `g`
- **Exit:** `q`

## Viewing Partial File Contents

### `head` - Display First Lines
Displays the first few lines of a file (default: 10 lines).

**Syntax:**
```bash
head -n <number> <filename>
# or
head -<number> <filename>
```

**Examples:**
```bash
head /etc/passwd          # First 10 lines
head -n 5 /etc/passwd     # First 5 lines
head -5 /etc/passwd       # First 5 lines
```

### `tail` - Display Last Lines
Displays the last few lines of a file (default: 10 lines).

**Syntax:**
```bash
tail -n <number> <filename>
# or
tail -<number> <filename>
```

**Examples:**
```bash
tail /etc/passwd          # Last 10 lines
tail -n 5 /etc/passwd     # Last 5 lines
tail -5 /etc/passwd       # Last 5 lines
```

## Counting File Elements

### `wc` - Word Count
Counts lines, words, and characters in a file.

**Syntax:**
```bash
wc [options] <filename>
```

**Options:**
- `-l`: Count lines only
- `-w`: Count words only
- `-c`: Count characters only

**Examples:**
```bash
wc /etc/passwd            # Shows all counts (lines, words, characters)
wc -l /etc/passwd         # Shows only line count
```

**Output format:** `lines words characters filename`

## Sorting File Content

### `sort` - Sort Lines
Sorts the lines of a text file.

**Syntax:**
```bash
sort [options] <filename>
```

**Common Options:**
- `-n`: Numeric sort
- `-r`: Reverse sort order
- `-k <field>`: Sort by specific field
- `-t <delimiter>`: Specify field separator

**Examples:**
```bash
sort /etc/passwd                    # Alphabetical sort (default)
sort -n -k 3 -t : /etc/passwd       # Numeric sort by UID (3rd field)
sort -n -k 3 -t : -r /etc/passwd    # Reverse numeric sort by UID
```

## Extracting Specific Content

### `cut` - Cut Out Sections
Extracts specific columns or characters from each line of a file.

**Syntax:**
```bash
cut [options] <filename>
```

**Options:**
- `-f <field_list>`: Specify field numbers (comma-separated or ranges)
- `-d <delimiter>`: Specify field delimiter
- `-c <character_list>`: Specify character positions

**Examples:**
```bash
# Extract usernames (1st field) from /etc/passwd
cut -f 1 -d : /etc/passwd

# Extract multiple fields
cut -f 1,3,6,7 -d : /etc/passwd

# Extract a range of fields
cut -f 1-4 -d : /etc/passwd

# Extract from field 4 to end
cut -f 4- -d : /etc/passwd

# Save output to file
cut -f 1,3,6,7 -d : /etc/passwd > passwd_selectedinfo

# Extract by character position
cut -c 1-3 testfile      # First 3 characters
cut -c 6- testfile       # From 6th character to end
```

## Using Pipelines

A pipeline (`|`) connects commands so the output of one becomes the input of the next.

**Examples:**
```bash
cut -f 1 -d : /etc/passwd | sort
cut -f 1 -d : /etc/passwd | sort | less
cat /etc/passwd | wc -l
```

## Finding Unique Lines

### `uniq` - Report or Filter Repeated Lines
Filters adjacent duplicate lines from sorted input.

**Syntax:**
```bash
uniq [options] <input>
```

**Common Option:**
- `-c`: Count occurrences of each line

**Examples:**
```bash
# First sort, then find unique lines
cut -f 1 -d : /etc/passwd | sort | uniq

# Count unique occurrences
cut -f 1 -d : /etc/passwd | sort | uniq -c
```

**Note:** `uniq` only removes adjacent duplicate lines, so input should typically be sorted first.

## Capturing Pipeline Output

### `tee` - Redirect to Multiple Destinations
Reads from standard input and writes to both standard output and files.

**Syntax:**
```bash
command | tee [file]
```

**Example:**
```bash
# Capture intermediate outputs in pipeline
cut -f 1 -d : /etc/passwd | tee cutoutput | sort | tee sortoutput | uniq -c
```

**Note:** Unlike redirection operators (`>` or `>>`), `tee` doesn't interrupt the pipeline flow.

## Pattern Matching

### `grep` - Global Regular Expression Print
Searches for patterns in files and displays matching lines.

**Syntax:**
```bash
grep [options] <pattern> <filename>
```

**Common Options:**
- `-v`: Invert match (show non-matching lines)
- `-c`: Count matching lines only
- `-r` or `-R`: Recursive search in directories
- `-i`: Case-insensitive search
- `-n`: Show line numbers
- `-E`: Extended regular expressions (same as `egrep`)

**Special Characters:**
- `^pattern`: Lines beginning with pattern
- `pattern$`: Lines ending with pattern
- `\.`: Escape special characters (e.g., `\.` for literal dot)

**Examples:**
```bash
# Basic search
grep root /etc/passwd

# Case-insensitive search
grep -i ava /etc/passwd

# Count matches
grep -c bash /etc/passwd

# Invert match
grep -v bash /etc/passwd

# Lines starting with pattern
grep ^root /etc/passwd

# Lines ending with pattern
grep bash$ /etc/passwd

# Search for special characters (escape with \)
grep '\#' /etc/bashrc

# Recursive search
grep -r bash ~

# Combine with other commands
grep bash /etc/passwd | cut -f 1 -d : | sort
```

### `egrep` - Extended Grep
Supports extended regular expressions (same as `grep -E`).

**Examples:**
```bash
# OR operation (matches lines containing 'bash' OR 'login')
egrep 'bash|login' /etc/passwd

# AND operation (matches lines containing 'no' followed by 'login')
egrep 'no.*login' /etc/passwd
```

## Comparing Files

### `diff` - Compare Files Line by Line
Shows differences between two files.

**Syntax:**
```bash
diff <file1> <file2>
```

**Output Format:**
- Lines prefixed with `<` are from the first file
- Lines prefixed with `>` are from the second file
- Line numbers indicate where changes occur

**Example:**
```bash
# Create two similar files with slight differences
echo "This is first line." > file1
echo "This is frist line." > file2
diff file1 file2
```

**Output interpretation:**
```
1c1
< This is first line.
---
> This is frist line.
```
This means line 1 in file1 was changed to produce line 1 in file2.

## Stream Editing

### `sed` - Stream Editor
Performs text transformations on an input stream.

**Syntax:**
```bash
sed 's/pattern/replacement/flags' <filename>
```

**Common Flags:**
- `g`: Replace all occurrences (global)
- `i`: Case-insensitive matching

**Examples:**
```bash
# Simple replacement (display only)
sed 's/nologin/yeslogin/g' /etc/passwd

# Save to new file
sed 's/nologin/yeslogin/g' database > newdatabase

# Multiple replacements
sed -e 's/nologin/YESLOGIN/g' -e 's/bash/zsh/g' database > multiple_changes

# In-place editing (with backup)
sed -i.bak 's/old/new/g' file.txt
```

**Note:** By default, `sed` outputs to stdout without modifying the original file.

## Pattern Scanning and Processing

### `awk` - Text Processing Language
Processes and analyzes text files, particularly structured data.

**Syntax:**
```bash
awk 'pattern { action }' <filename>
```

**Common Usage:**
- `$1`, `$2`, ...: Represent fields in the current line
- `NF`: Number of fields in current line
- `NR`: Current line number
- `FS`: Field separator (default: whitespace)
- `OFS`: Output field separator (default: space)

**Examples:**
```bash
# Create a sample file
echo "echo hello world!" > helloworld

# Print specific fields with custom separator
awk '{print $1 ";" $2 ";" $3}' helloworld

# Reorder fields
awk '{print $3 ";" $2 ";" $1}' helloworld

# Specify input field separator
awk -F: '{print $1, $3}' /etc/passwd

# Conditional processing
awk -F: '$3 > 1000 {print $1}' /etc/passwd
```

**Note:** By default, `awk` uses whitespace as the field separator. Use `-F` to specify a different separator.

---
