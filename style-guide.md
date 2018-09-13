# Ajanke Bash Style Guide

This style guide is based on:
- Google's Shell Style Guide (https://google.github.io/styleguide/shell.xml)
- Years of experience writing Bash for programatic efficiency, readablity, and maintainablity

## File Handling Standards
- Executables should have no extension (strongly preferred) or a `.sh` extension to enable editor syntax highlighting
- Scripts that use Bash 4.x specific features should run a check to ensure users invoke the appropriate version
- Libraries must have a `.sh` extension and should not be executable
- Executables/scripts should be run with the least level of priviledge required; avoid `root` whenever possible


## Security Standards
- If superuser priviledges are required, rewrite the program not to require superuser priviledges
- If script REALLY requires superuser priviledges, use `sudo` inline and validate priviledges by checking `${EUID}`
- Sanitize all inputs using Bash's substring manipulation features (or external utils when required)
- Validate the format of inputs using Bash's substring manipulation features (or external utils when required)
- Make functions and variables as read-only using the `readonly` or `declare` builtin whenever possible
- Scope variables to `local` whenever possible
- Contain all variable and array declarations within functions to facilitate unit testing, whenever possible
- Unit test code to validate security precautions as soon as reasonably possible in the development process


## Data Flow Standards
- Prefer a declarative, functional programming paragdim for anything other than the most trivial of scripts
- Avoid spawning subshells, subprocesses, and calling external binaries whenever reasonable
- Given the option of using a `builtin` or an external binary, default to the `builtin` whenever possible
- Errors go to STDERR and output goes to STDOUT unless there is a very compelling reason to change that
- Functions should handle errors and return useful return codes and output for each condition in their logic tree
- Avoid only handling conditions that follow the "happy path" where edge cases don't exist and input self-validates


## Formatting Standards
- NO TABS
- End lines or commands with an explicit `;` character rather than relying on line endings to close-out statements
- Always indent the contents of functions two spaces from the start of the line that declares the function

```bash
myFunction() {
  echo "myFunction printed this line to STDOUT.";
}
```

- Do not exceed 80 characters per line whenever possible
- Should a single command plus arguments exceed 80 characters, use `\` to wrap the line, breaking between each argument
- When using the escape character to escape a newline (/n), always place a space before the escape character
- Whenever a single command plus its arguments are line-wrapped, indent the arguments to show subordination

```bash
find \
  /var/log/ \
  -type f \
  -name "*.log" \
  -size +250M \
  -mtime 30;
```
- When using a loop, indent the contents 2 spaces to show subordination; repeat pattern for nested loops
- When using a `while`, `until`, or `for` loops (without a list), place the `do` statement behind the `;` on the first line
- Whenever a pipe (|) is used, wrap the line, and then indent the wrapped line to show subordination

```bash
grep "some_line" somefile.txt \
  | while read line; do
      count+=1;
    done;
echo "found some_line in file: ${count} times";
```

- Whenever a list is specified with a `for` loop, place a line wrap (\) at end of first line before any list items
- Whenever a list is specified with a `for` loop, place one argument per line, indented to start at first line wrap
- Whenever a list is specified with a `for` loop, place the `;` on seperate a line indented to match line wrap on first line
- Whenever a list is specified with a `for` loop, place the `do` statement on seperate a line indented to match `for`
- Whenever a list is specified with a `for` loop, indent commands after `do` line 3 spaces
- Whenever a list is specified with a `for` loop, place the `done` statement on seperate a line indented to match `for`

```bash
for bin in \
           ls \
           grep \
           cd \
           uname \
           ;
do
   which ${bin} \
   || echo "binary not found";
done;
```

- Whenever a `case` statement is used, indent cases and commands 2 spaces and seperate cases with a `;;` line

```bash
case ${arg} in
  [0-9])
     echo "arg is a number";
     ;;
  true|false)
     echo "arg is a boolean";
     ;;
  [a-z]|[A-Z])
     echo "arg is a alphabetical character";
     ;;
  *)
     echo "arg is not alphanumeric or a boolean" \
     && return 1;
     ;;
esac;
```


- Whenever the `and` (&&) and `or` (||) operators are used, wrap the line before using the operator; indent to match previous command
- Whenever grouping commands with `and`/`or` operators, place the curly braces on their own lines 
- Whenever grouping commands with `and`/`or` operators, indent the commands in line with line wrap after the opening curly brace

```bash
{ ps -aef \
    | grep "some_line" \
  && echo "found some_line";
} \
|| { \
     echo "could not find some_line.";
     return 1;
   };
```

## Naming Standards

In general, labeling and naming should follow the Perl paradigm of "things that are different should look different."
The details of that can vary by project and team, but should remain consistent across the entirety of a single set of programs or libraries.

### Variables

- Variable naming should utilize the underscore character (_) or mixed casing to breakup name strings into human-readable words
- Using curly braces when calling variables is preferred to not using them (`${USER}` vs `$USER`)
- Variable naming should avoid using all caps (`${USER}`) to avoid collision with global variables exported from the parent shell's `env` outside the program
- Variable names should look very distinct from function names
- Variable types (i.e. local, read-only, exported) should all look similar to one another when compared to function names
- Variable types (i.e. local, read-only, exported) should look different from one another in a consistent way

### Arrays
- Arrays should follow a similar naming schema to variables, and look distinct from functions
- Arrays should be flagged in a way that differentiates them from normal variables (i.e. a literal "_array" at end of name)

### Functions
- Functions should use name spacing to indicat functions that are interdependent and/or were sourced from the same library file
- Name spacing should be implemented using either (_) or mixed casing to breakup name strings into human-readable words
