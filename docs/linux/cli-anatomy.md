# Anatomy of the command line

This section is dedicating for the understanding of the different parts of the
command line environment and learning their names.

## The command prompt

This is the text before your cursor on a new line, like this:
```
user@computer:/current/path$
```

This line usually contains some useful information:

- username
- computer name (`scomp1641` on moya)
- current location (what you would get with `pwd`)

## Command

In general, a command is composed of the three following elements separated by
one or more space characters: a **name**, a list of **options** also called **flags** and a
list of **arguments**.

The **name**, which always comes first, is indeed the name of the program we want to run.

The options, are parameters used to influence the program’s behaviour and are
usually easy to recognize as they are always preceded by a dash or a double dash.
(option that is a single word without giving some extra value is called a flag,
like `-h`)

Finally, the **arguments**, are values the program will use as an input, like a number,
a file name or text string for example.

Sometimes everything can be described as argument following the command's name.
These can be personal preferences.

In general, an option and flag influence how a program functions (think of
buttons on a machine).

A simple and commonly used command is `ls`.
Here is an edit version of what you get if you type `ls --help` (passing the help flag):

```
Usage: ls [OPTION]... [FILE]...
List information about the FILEs (the current directory by default).
Sort entries alphabetically if none of -cftuvSUX nor --sort is specified.

Mandatory arguments to long options are mandatory for short options too.
  -a, --all                  do not ignore entries starting with .
  -A, --almost-all           do not list implied . and ..
  -h, --human-readable       with -l and -s, print sizes like 1K 234M 2G etc.
  -l                         use a long listing format
      --help     display this help and exit
      --version  output version information and exit
```

The help message uses the word option, and then specifies that it expects files (also folders) as arguments.

In the most cases, `[`&`]` indicate optional arguments, while `<` & `>` may indicate necessery ones.

In the case of `ls`, you can think of the arguments (files) as targets on which the action should be performed,
while the options/flags specify how it should be done.

For classical commands, you can usually combine multiple flags in "one word":
```
ls -l -h
ls -lh
```

Both do the same. But this isn't true for all commands/programs.

## Running programs

Every command is a program. Script and program can be used interchangeably usually.

When you run a program, you **execute** it. Another term for it is **invocation**.


## Key commands

- `^c` (`ctrl` + `c`) - kill the current program
- `^z` - pause the current program (you can start it up again with `fg %1`; [see](https://git.wur.nl/microbiome/tool-docs/-/blob/master/bioint-tools/docs/linux/process-management.md))
- `^d` - send an EOF (End-of-file) marker
    + This terminates an interactive session and quits
- `^l` - Clear the screen
- `^k` - Delete everything after the cursor
 
For more, check this [page](https://git.wur.nl/microbiome/tool-docs/-/blob/master/bioint-tools/docs/linux/shortcuts.md)
