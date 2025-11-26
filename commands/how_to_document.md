# Guidelines for Documenting Unix Commands

+ Place all notes for a single command in a directory named after the command (for example, documentation for the `ls` command should be stored in the `ls` directory).
+ Record the command overview and syntax in the directory’s README file.
+ Create a subdirectory for each sub-command. Name the subdirectory after the sub-command (for example, document `git commit` in a `commit` directory inside the `git` directory).
+ In each sub-command directory, describe the sub-command overview and syntax in its README file.
+ Document each flag or switch in a separate file. Place the file in the main command directory if the flag belongs to the base command, or in the relevant sub-command directory if it applies only to that sub-command.
+ Use a consistent format for all flag or switch files and include one or more usage examples.
+ If the command accepts inputs that do not require switches, document them in a file named `inputs.md`. Maintain a consistent format for these inputs.
+ Document any other information in `extra.md`.
