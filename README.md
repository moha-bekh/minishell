# Minishell

`minishell` is a Unix shell written in C, developed as part of the 42 curriculum.
The goal of the project is to reproduce core shell behavior: parsing user input,
expanding variables and wildcards, handling redirections and pipes, executing
programs, and keeping interactive signal behavior close to Bash.

## Why this project matters

Building a shell requires more than launching processes. This project touches
low-level Unix concepts that are central to systems programming:

- process creation with `fork`, `execve`, `waitpid`
- pipe orchestration and file descriptor lifecycle management
- input parsing, tokenization, syntax validation, and command tree construction
- environment management and variable expansion
- terminal behavior, signals, and interactive readline handling
- memory ownership across linked lists, command structures, and execution paths

## Features

- Interactive prompt with command history through GNU Readline
- External command execution using the `PATH` environment
- Builtins:
  - `cd`
  - `echo`
  - `env`
  - `exit`
  - `export`
  - `pwd`
  - `unset`
- Pipelines with `|`
- Input, output, append, and heredoc redirections:
  - `<`
  - `>`
  - `>>`
  - `<<`
- Environment variable expansion, including `$?`
- Quote handling for single and double quotes
- Wildcard expansion with `*`
- Conditional execution with `&&` and `||`
- Subshell execution with parentheses
- Signal handling for interactive usage, especially `Ctrl-C`, `Ctrl-\`, and EOF

## Architecture

The code is split into focused modules:

```text
src/
|-- built_in/   # Shell builtins
|-- data/       # Global shell state, environment, cleanup
|-- exec/       # Command execution, pipes, redirections
|-- expand/     # Variables, wildcards, heredoc expansion
|-- parsing/    # Command and redirection parsing
|-- signal/     # Interactive signal behavior
|-- token/      # Tokenization and syntax checks
|-- tree/       # Execution tree for logical operators and subshells
`-- utils/      # Shared helpers
```

Execution flow:

```text
readline
   -> tokenization
   -> expansion
   -> syntax and command parsing
   -> execution tree
   -> fork/exec, builtins, pipes, redirections
   -> cleanup and next prompt
```

## Build

### Requirements

- `cc` or `clang`
- `make`
- GNU Readline

On macOS, GNU Readline can be installed with Homebrew:

```sh
brew install readline
```

The Makefile currently links Readline from `/usr/local/include` and
`/usr/local/lib`. If your installation is under `/opt/homebrew`, update the
Readline include and library paths in the Makefile.

### Compile

```sh
make
```

### Run

```sh
./minishell
```

### Clean

```sh
make clean
make fclean
make re
```

## Usage examples

```sh
minishell$ echo hello world
hello world

minishell$ export NAME=Moha
minishell$ echo "Hello $NAME"
Hello Moha

minishell$ ls -la | grep minishell

minishell$ cat << EOF > out.txt
hello from heredoc
EOF

minishell$ false || echo "fallback"
fallback

minishell$ (cd /tmp && pwd)
/tmp
```

## Technical highlights

- Custom lexer/parser that separates token processing from command execution
- Binary tree representation for logical operators and subshell groups
- Builtins executed in the parent process when they need to mutate shell state
- Dedicated expansion layer for variables, quotes, heredocs, and wildcards
- Centralized cleanup routines to reduce leaks across error paths
- Signal behavior adapted for parent shell, child processes, and heredoc input

## Project constraints

This project follows the 42 school constraints:

- implemented in C
- custom `libft` used for common utility functions
- no use of high-level shell execution wrappers
- careful memory management and explicit resource cleanup

## Status

The main shell behavior is implemented. The project is intended as a systems
programming exercise and a portfolio piece demonstrating Unix process control,
parsing, and terminal programming in C.
