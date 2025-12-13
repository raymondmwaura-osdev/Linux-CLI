## `PROMPT`

`PROMPT` in the Z shell (zsh) is the primary prompt string parameter that defines the text and structure displayed by the shell immediately before reading each command line. It functions as zsh’s equivalent of the POSIX `PS1` prompt variable, and is central to the shell’s user interface model for interactive use. When an interactive zsh session is running, the value of `PROMPT` is subject to *prompt expansion*: special sequences beginning with `%` are evaluated and replaced with dynamic information (e.g., current directory, username, exit status, host name) each time the prompt is rendered. `PROMPT` can be customized by users and themes to enhance contextual feedback in command‑line workflows.

**Default Value**
The default value of `PROMPT` in a standard zsh installation is:

```zsh
%m%#
```

Here `%m` expands to the short hostname (up to the first dot) and `%#` expands to `%` for a regular user or `#` for a superuser.

### Data Type and Structure

`PROMPT` is a *scalar string parameter*. The string may contain literal characters as well as special percent‑escape sequences recognized by zsh’s prompt expansion mechanism. These sequences begin with `%` followed by a letter or symbol, and are evaluated at display time. The parameter is classified as a zsh parameter of type `<S> <Z>` (scalar, zsh‑specific). As such, it behaves like other string parameters but undergoes *prompt expansion* rather than simple parameter expansion when used to render the interactive prompt.

### Expansion and Evaluation Semantics

`PROMPT` undergoes **prompt expansion** at the moment the shell is ready to draw the prompt for the user. Prompt expansion is distinct from ordinary parameter expansion: percent escapes such as `%~` (current working directory), `%n` (username), and `%#` (prompt character) are dynamically replaced with their evaluated values each time a prompt is rendered. Prompt expansion honors the current shell state, including working directory, history position, last command exit status, user identity, and other contextual information. Control over whether additional parameter substitutions occur in prompts can be influenced by options such as `PROMPT_SUBST` (when set, enables full parameter substitution inside prompt strings).

### Configuration and Customization

Common configuration patterns involve assigning a new prompt string to `PROMPT` in a startup file such as `~/.zshrc`. For example:

```sh
PROMPT='%n@%m %~ %# '
```

This sets a prompt showing `username@hostname`, the abbreviated current working directory (`%~`), and a `%` or `#` prompt symbol. Multiple sequences may be combined along with literal text to tailor the prompt to individual preferences. Configuration can also include ANSI color escapes via `%F{color}` and `%f` pairs.

Custom prompt definitions should appear *after* any theme or framework initialization (e.g., Oh‑My‑Zsh) to avoid being overridden. Third‑party prompt functions (invoked via `promptinit` and the `prompt` builtin) may also alter `PROMPT`, and overriding them often requires placing custom assignments at the end of `~/.zshrc` after sourcing such frameworks.

### Interactions and Dependencies

`PROMPT` interacts with several zsh mechanisms and other variables:

* **Percent‑escape sequences**: It is tightly coupled with zsh’s prompt expansion subsystem; sequences such as `%n`, `%~`, `%m`, `%T`, and `%#` are expanded at runtime.
* **`RPROMPT`**: A separate variable that controls a right‑aligned prompt, using the same expansion semantics.
* **Shell options**: Options such as `PROMPT_SUBST` influence how parameter and command substitutions are treated within the prompt string.
* **Prompt frameworks/themes**: Mechanisms like `promptinit` and theme systems in frameworks such as Oh‑My‑Zsh may override or generate `PROMPT` definitions; these often interact with `PROMPT` assignments and must be accounted for in customization workflows.
* **Secondary prompts**: Additional related parameters (`PROMPT2`, `PROMPT3`, `PROMPT4`) control prompts used in multi‑line and select/trace contexts, and follow similar expansion semantics.

### Examples

**Example 1: Simple Custom Prompt**

```sh
# ~/.zshrc
PROMPT='%n@%m %~ %# '
```

This configuration yields a prompt like `user@hostname ~ %`, showing the username, hostname, current abbreviated directory, and a prompt character.

**Example 2: Adding Date and Time**

```sh
PROMPT='%n@%m %T %~ %# '
```

This incorporates the current time (`%T`) into the prompt, providing temporal context for each new command line.

---
