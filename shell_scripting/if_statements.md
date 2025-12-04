# if statements

An if statement lets your script **make decisions**: it tests a condition (or the result of a command) and (based on whether that condition is true or false) executes different code blocks. This control-flow ability is fundamental to writing scripts that behave differently under different circumstances.

In Bash (and many other shells), conditions are evaluated based on command exit status (0 means success/true, non-zero means failure/false) or via built-in test operators.

---

## Basic Syntax

The simplest form of an if statement has this structure:

```bash
#!/bin/bash

if [ CONDITION ]; then
    # commands to run if CONDITION is true
fi
```

* `if` marks the beginning of the conditional.
* The condition is enclosed in brackets `[...]` (or alternative syntaxes) which evaluate to true or false.
* `then` precedes the block of code that should execute if the condition is true.
* `fi` (that’s `if` backwards) marks the end of the if block.

**Example; checking a number:**

```bash
#!/bin/bash

num=15
if [ $num -gt 10 ]; then
    echo "Number is greater than 10."
fi
```

If you run this, you'll get “Number is greater than 10.” because the condition (`num > 10`) is true.

---

## `if … else`

Often you want to do something if a condition is true *and* something else if it’s false. For that you use `else`.

Syntax:

```bash
if [ CONDITION ]; then
    # code if CONDITION is true
else
    # code if CONDITION is false
fi
```

**Example; number comparison:**

```bash
#!/bin/bash

read -p "Enter a number: " num

if [ $num -gt 10 ]; then
    echo "Number is greater than 10."
else
    echo "Number is 10 or less."
fi
```

If `num` is 8, the script prints “Number is 10 or less.”

---

## `if … elif … else`

When you have more than two conditions to check, you can use `elif`. This lets you test multiple conditions in sequence, executing the block for the first matching condition.

Syntax:

```bash
if [ CONDITION1 ]; then
    # code if CONDITION1 is true
elif [ CONDITION2 ]; then
    # code if CONDITION2 is true
else
    # code if none of the above conditions are true
fi
```

You can include multiple `elif` branches. Only the first true condition’s block executes; the rest are skipped.

**Example; check number sign:**

```bash
#!/bin/bash

read -p "Enter a number: " num

if [ $num -gt 0 ]; then
    echo "Positive"
elif [ $num -lt 0 ]; then
    echo "Negative"
else
    echo "Zero"
fi
```

Depending on the user’s input, this script will print whether the number is positive, negative, or zero.

---

## Variants and Important Details: Condition Syntax, Operators, Best Practices

### Condition Syntax Variants

* Single-bracket: `[ condition ]`; most portable (POSIX-compliant).
* Double-bracket: `[[ condition ]]`; extended test syntax, supported in modern shells like Bash, often safer for string comparisons and pattern matching.
* For numeric comparisons, you use test operators like `-eq`, `-lt`, `-gt`, etc.

### Logical Operators

You can combine conditions using logical OR (`||`) or AND (`&&`); for example to test if a variable equals one of several values.

**Example (OR):**

```bash
if [ "$var" = "foo" ] || [ "$var" = "bar" ]; then
    echo "var is either foo or bar"
fi
```

When using double-bracket syntax in Bash:

```bash
if [[ "$var" == "foo" || "$var" == "bar" ]]; then
    echo "var is either foo or bar"
fi
```

### Nested `if` Statements

You can use an `if` inside another `if` to support more complex logic:

```bash
if [ CONDITION1 ]; then
    if [ CONDITION2 ]; then
        # code if both conditions true
    fi
fi
```

But deep nesting can reduce readability; often better to reshape logic or split into separate functions/scripts.

### Best Practices

* Quote variables when testing strings, especially if there may be spaces or empty values: e.g. `if [ "$str" = "something" ] …`.
* Use indentation to improve readability.
* Use the portable single-bracket syntax if you aim for compatibility across different POSIX-compliant shells.
* Test your scripts thoroughly; especially when dealing with user input or file system operations.

---

## Common Use-Cases / Examples

Here are several typical scenarios where `if` statements are used in shell scripting:

* **Check if file exists**:

```bash
if [ -e file.txt ]; then
    echo "file exists"
else
    echo "file does not exist"
fi
```

- **Check execute permission before running a script**:  

```bash
if [ -x ./myscript.sh ]; then
    ./myscript.sh
else
    echo "myscript.sh is not executable"
fi
```

- **Check if a string is empty or not**:  

```bash
read -p "Enter something: " input
if [ -z "$input" ]; then
    echo "Input was empty"
else
    echo "You entered: $input"
fi
```

- **User input validation / branching logic** (e.g. check user role, configuration params, runtime environment, etc.) — ideal for scripting automation or admin tasks.  

---

## A Full Example Script

Here is a somewhat realistic script combining several concepts: user input + numeric comparison + file-existence check + branching.  

```bash
#!/bin/bash

echo -n "Enter a number: "
read num

if ! [[ "$num" =~ ^[0-9]+$ ]]; then
    echo "Error: not a valid positive integer."
    exit 1
fi

if [ $num -eq 0 ]; then
    echo "You entered zero."
elif [ $num -gt 0 ]; then
    echo "You entered a positive number."

    file="output_$num.txt"

    if [ -e "$file" ]; then
        echo "File '$file' already exists."
    else
        touch "$file"
        echo "File '$file' created."
    fi

else
    # This branch won't actually execute because we validated num >= 0
    echo "You entered a negative number."
fi
```

This script demonstrates: input reading, validation, numeric condition, branching, file-existence test, nested `if`.

---
