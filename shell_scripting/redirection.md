# Redirection

## What is redirection, and what are “streams”

When you run a command in a shell, by default:

* Input comes from **standard input** (stdin, file descriptor 0; often keyboard).
* Normal output goes to **standard output** (stdout, file descriptor 1; typically the terminal screen).
* Error messages go to **standard error** (stderr, file descriptor 2; also typically the terminal).

Redirection is the mechanism by which you tell the shell to reroute these streams: instead of reading from keyboard, write to screen, write errors to screen; you can make them read from or write to files (or other descriptors).

---

## Basic Redirection Operators

### Redirecting Standard Output; `>` and `>>`

* `>`: Redirect standard output (stdout) to a file. If the file doesn’t exist; it’s created. If it exists; it is truncated (i.e. overwritten).
* `>>`: Redirect stdout and **append** to the file (if exists); do not overwrite. If file doesn’t exist; create it.

**Example:**

```bash
echo "Hello, world" > greeting.txt     # greeting.txt will contain exactly "Hello, world"
echo "Another line" >> greeting.txt    # greeting.txt now has two lines
```

By default, using just `>` without a number prefix refers to file descriptor 1 (stdout).

You can prefix with a specific file descriptor, e.g. `1>file`, though `>` alone is shorthand for `1>`.

---

### Redirecting Standard Input; `<`

* `< filename` or `command < filename`: Redirect standard input (stdin, file descriptor 0) so that instead of keyboard input, the command reads from the given file.

**Example:**

```bash
sort < unsorted_list.txt > sorted_list.txt
```

Here, `sort` reads data from `unsorted_list.txt` (stdin redirected), sorts it, then writes output to `sorted_list.txt`.

---

## Redirecting Standard Error and Combining Streams

Sometimes commands produce both normal output and error messages; you may want to separate them, combine them, or suppress them.

### Redirecting stderr only: `2>`, `2>>`

* `2> error.log`: Redirect standard error (stderr, fd 2) to file `error.log` (overwrite/truncate).
* `2>> error.log`: Redirect stderr and append to `error.log`.

**Example:**

```bash
grep "pattern" file1 file2 > matches.txt 2> errors.log
```

Normal (matched) output goes to `matches.txt`; error messages (e.g. “file2: No such file or directory”) go to `errors.log`.

### Redirecting both stdout and stderr to the same file

There are a few common patterns:

* `command &> output.log`; in Bash, redirects both stdout and stderr to `output.log`.
* Alternatively: `command > output.log 2>&1`; redirect stdout to file, then redirect stderr (fd 2) to wherever stdout (fd 1) is currently going (i.e. the file).

**Example:**

```bash
ls /some/directory &> all_output.log
# or
ls /some/directory > all_output.log 2>&1
```

Both stdout and stderr end up in `all_output.log`.

### Appending both stdout and stderr

Use:

```bash
command >> output.log 2>&1
```

or (in Bash with &>>):

```bash
command &>> output.log
```

---

## Advanced Redirection: Here-Documents, Here-Strings, and File Descriptor Duplication

### Here-Documents (`<<`) and Here-Strings (`<<<`)

- **Here-Document**: `command <<EOF` then lines, then `EOF`. The shell reads all lines up to the delimiter and feeds them as stdin to the command. Useful to supply multiline input inline.
- **Here-String** (in Bash): `command <<< "some string"`; passes the given string (plus newline) to command’s stdin.

**Example (here-document):**

```bash
cat <<EOF > myfile.txt
Line 1
Line 2
EOF
```

This writes the two lines into `myfile.txt`.

### Duplicating and Moving File Descriptors

You can duplicate a file descriptor or redirect one descriptor to another. For example:

* `2>&1`: make stderr (fd 2) go to the same place as stdout (fd 1).
* You can also explicitly redirect other (nonstandard) descriptors, or close descriptors.
* Other forms: `<&`, `>&`, `<&-`, `>&-`, for duplicating or closing descriptors, or redirecting them.
* Opening a file for both read and write on a descriptor: e.g. `n<> file`.

These features support advanced scripting: managing multiple streams, piping through descriptors, building flexible I/O schemes.

---

## Order of Redirections Matters

In many shells (Bash), redirections are processed from left to right. This can affect meaning when combining multiple redirections.

For example:

```bash
command > out.txt 2>&1
```

This redirects stdout to `out.txt`, then redirects stderr to “wherever stdout now goes” (i.e. the file).

If you reversed:

```bash
command 2>&1 > out.txt
```

Then stderr is first duplicated to the current stdout (terminal), **then** stdout is redirected to the file. Result: stdout goes to file, stderr goes to terminal.

Thus, redirection ordering is non-commutative; order matters.

---

## Common Use Cases and Examples

Here are examples illustrating typical redirection use cases:

* **Basic output capture**:

  ```bash
  date > now.txt
  ```

* **Appending logs**:

  ```bash
  echo "Run at $(date)" >> run.log
  ```

* **Redirect input from a file**:

  ```bash
  sort < unsorted.txt > sorted.txt
  ```

* **Separate stdout and stderr into different files**:

  ```bash
  some_command > output.log 2> error.log
  ```

* **Combine both stdout and stderr into one log file**:

  ```bash
  some_command &> combined.log
  # or
  some_command > combined.log 2>&1
  ```

* **Suppress all output (discard both stdout and stderr)**:

  ```bash
  some_command > /dev/null 2>&1
  ```

* **Provide multiline input inline (here-document)**:

  ```bash
  cat <<EOF > file.txt
  line one
  line two
  EOF
  ```

* **Pass a short string as stdin (here-string)**:

  ```bash
  grep "foo" <<< "foo bar baz"
  ```

* **Complex redirection with multiple descriptors (rare but powerful)**:

  ```bash
  exec 3>logfile        # open fd 3 for writing to logfile
  some_command >&3      # redirect stdout to fd 3
  ```

---
