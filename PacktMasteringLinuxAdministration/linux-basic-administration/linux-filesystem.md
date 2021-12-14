# The Linux Filesystem

## Introducing the Linux shell

### Bash Shell Features

#### Wildcards and Meta-characters

Wildcards:

- Asterisk (*): match any string of none or more characters
- Question mark (?): match a single character
- Brackets ([]): match any of the characters inside the brackets

Meta-characters:

- **>**: Output redirection
- **>>**: Output redirection - append
- **<**: Input redirection
- **<<**: Input redirection
- **|**: pipe
- **;**: command execution sequence
- **()**: group of commands in the execution sequence.
- **||**: conditional or
- **&&**: ocnditional and
- **&**: run a command in the background
- **#**: use a command directly in the shell
- **$**: variable value expansion
- **\`cmd\`**: command substitution
- **$(cmd)**: command substitution

Example:

```bash
# use the output of one command inside another command
$ ls -l `which ls`

# pipe result to less
$ ls -l /etc | less

# multiple command
$ whoami; pwd;

# redirect output to file
$ (whoami; pwd;) > file
```

#### Brace expansion

Example:

```bash
# delete two file
$ rm ~/xpackt/{report,new-report}

# create sequence of 5 files
$ touch file{1..5}
```

#### Bash shell variables

some built-in variables

HOME, LOGNAME, PWD, OLDPWD, PATH, SHELL, USER, TERM

Example:

```bash
# call variable
$ echo $SHELL

# assign variable
$ MYVAR=sysadmin
$ echo $MYVAR

# list all the shell variables
$ printenv | less
```
