# Linux Basics: [grep](#filtering-grep), [sort](#sort), [uniq](#uniq), [cut](#cut-filter-columns), [paste](#paste-combine-columns), [wc](#wc-word-count), [diff](#diff-whats-the-difference)

In this section, you will learn some basic operations that will change your life.

## Filtering: `grep`

One of the basic questions we want to answer if a file (or stream; e.g. STDIN) contians
an expression or not, or to keep the lines that contain it or count them.
These oprations can be done by `grep`.

Let's start with the most basic form, when you want to find an exact match of some text in your file.
We will take the example of a FASTA file to learn some basics. So the first question is,
Keep the ID lines in the FASTA files. (All ID lines start with `>`.)

> We are going to use a FASTA file that we download from [GRAbB Github repo](https://github.com/b-brankovics/grabb):
>
> `wget https://raw.githubusercontent.com/b-brankovics/grabb/master/data/references/published_mts.fas` will save it to your current working directory.

```bash
# reading from the file
grep '>' published_mts.fas >ids
# reading from STDIN
cat published_mts.fas | grep '>' >ids
```

This short code saves all the ID lines to the file `ids`. We used `'` (quotes for the pattern, because `>` has a meaning in bash to save the output to a file, like `>ids`).

**Counting**
In the case of FASTA files a more common question is "_how many sequences are there?_"
To answer this, we only need to tell grep that we want a count and not the lines
by passing the `-c` argument (or flag).

```bash
grep -c '>' published_mts.fas
```

> One note on argument order. On Ubuntu it doesn't matter where you place the flag,
> but on Mac it may matter. Check the `--help` or `man` of the command to check proper order.

**Reverse**
Frequently, you want to skip certain entries. This can be done using the `-v` flag.
This flag inverts the results, so all lines that would be kept normally will be filterd out,
and non-matching lines are printed.

```bash
grep -v 'oxy' ids
# Or without quotes is alsp good
grep -v oxy ids
```

**Patterns** Until now we used exact matches where the given text matches a part of the text
in the file, but the real power commes from **patterns** or regular expressions (or **regex**).
There are many dialects of regular expressions, but luckily the regex of Perl is very widely used
and many programs or programming languages also know it. So learning the one for Perl will take
you a long way. Just remember to tell program or script you are using that you want to use
Perl's regex grammar, and you're good.

> To learn more about regex (regular expressions) and pattern matching, check out the
> [next linux section](linux-basics-03.md).

For a brief example, let's say that each definition line contains a string (text) like `len=134`.
We could generalize this into the following pattern `len=` followed by a number, which is
frequently written as `len=<num>` (or `len=<int>`) in command descriptions. That is also a pattern.
Probably most regex grammers recognize the following as a number `[0-9]` (or `[0123456789]`),
basically a list of the possible characters. In Perl regex there is a shorthand `\d`.
Let's say we want to select all the sequence IDs for which length is at least 100 (or three
digits). So for this what we need is to tell that it has to be at least three numbers. The
simplest solution is `len=\d\d\d` (a nicer version is `len=\d{3}` or more explicitly `len=\d{3,}`).

```bash
# -P tells grep to use Perl type regex
grep -P "len=\d\d\d" contigs.fas >long.fas
```

**Context matters.** You can even ask for some context arround your hits.
There are three options:

- before (`-B <int>`)
- after (`-A <int>`)
- and around (`-C <int>`): basically the like specifying both `-A` and `-B` with the same value

So going back to the FASTA example. If your file is formated so that each sequences is exactly
one line long, then you can get the full sequences of all your _E. coli_ strains if `E. coli`
is in the seqence definition

```bash
grep -A 1 "E. coli" my.fasta | lesss
```

You can see that the results are separated by lines that consist of `--`.
Which is handy when you want to explore the context, but in our case it breakes
the FASTA format.

```bash
# We add an extra grep step to filter those lines out
grep -A 1 "E. coli" my.fasta | grep -v -P '^--$' >ecloi.fas
```

## `sort`

The sort command sorts the contents of a file, in numeric or alphabetic order,
and prints the results to standard output (usually the terminal screen). The
original file is unaffected.

flags and sort types:

- `-u` - (unique) remove duplicates
- `-f` - sort case-insensitively
- `-r` - reverse order
- `-V` - version-sort
- `-n` - general numeric
- `-h` - human numeric (e.g., 2K 1G)
- `-M` - month
- `-R` - random shuffle, but group identical keys

You can sort by specific column(s), using the `-k` or `--key=KEYDEF` argument.
For example if you want to sort a file based on the second column, then you can
type `sort -k2 file` or `sort -k2,2 file`. The difference between the two is
that the second on **only** sorts based on the second column.
If you want to sort based on multiple columns, for example sort the first
column based on default alphanumeric and the second in reversed numeric,
then you need `sort -k1,1 -k2,2nr file`. The order of the terms defines the
priority of the sorting.

Some times you need to specify the field separator:
 
- `-t` or `--field-separator=SEP` - e.g. `-t "\t"` for tabs

## `uniq`

The `uniq` command in Linux is a command-line utility that reports or filters
out the repeated lines in a file. In simple words, uniq is the tool that helps
to detect the adjacent duplicate lines and also (by default) deletes the
duplicate lines. The command does not modify the input file.

Just remember that `uniq` does not sort the input itself. So if every other
line is the same they count as 'unique' (e.g. "odd, even, odd, even" would
not count as duplicate.)

Flags:

- `-c` - counts the number of times a given line was repeated after each other.
- `-d` - display only duplicate lines (similar to filtering `-c` for number higher than 1)
- `-u` - print only unique lines (removes duplicate lines)
- `-i` - ignore case

You can also modify which parts of the input are compared:

- `-f N` - It allows you to skip **N** fields(a field is a group of characters, delimited by whitespace) of a line before determining the uniqueness of a line
- `-s N` - It doesn’t compare the first **N** characters of each line while determining uniqueness. This is like the `-f` option, but it skips individual characters rather than fields
- `-w N` - It only compares **N** characters in a line

## `cut` - 'filter' columns

The cut command can be used to limit the number of columns (fields) that are show for the
input. This is very useful when you have larger tables and you only want to see specific
info for the lines.



It is also useful to check the composition of given columns, to know what kind of values
are in the 5th column:

```
cut -f5 file | sort | uniq -c
```

Here we are using pipe's to combine the power of the different commands to solve a
problem. Since we use `-c` flag with `uniq`, we also can see how many times a given
value shows up in the 5th column (`cut -f5`). Because `uniq` counts only consecutive
lines, we need to add a `sort` before.

## `paste` - combine columns

Paste is kind of the reverse of `cut`. Or a complement of `cat`.
Instead of conecatenating files after each other from top to bottom, it adds the
new content as new columns.

## `wc` - word count

This handy command can be used to count words, lines or characters. For biology
related command line usage, probably count lines is the most important.
This can give you info on the file size, but sometimes it is directly a biologically
relevant information. If you have a table with positive hits, then you can look
at the number of hits simply by the line number. Just remember to check if
there is a header line or if it ends with a new line character or not, so
you calculate correctly.

```
wc -l file
```

This command will tell you how many lines `file` has.

## `diff` - what's the difference?

Diff is a great help in comparing files. The default is that shows you the differences
and shows nothing if they are the same. There are two types of differences:
a line that is only present in one of the files, or a line that is different in the two
files. It only shows difference on line level and will not highlight further differences.

If you want to have a clear indications that two files are identical then use the
`-s` flag.

If you want to see the differences in more context, then you can use the `-y` option.
In this mode it will show the conetents side by side.
. The default is that shows you the differences
and shows nothing if they are the same. There are two types of differences:
a line that is only present in one of the files, or a line that is different in the two
files. It only shows difference on line level and will not highlight further differences.

If you want to have a clear indications that two files are identical then use the
`-s` flag.

If you want to see the differences in more context, then you can use the `-y` option.
In this mode it will show the conetents side by side.

## Some extras

These were some of the extra commands that can make your life so much easier on the
command line, especially if you are working on data analysis.

Also remember the tools that you may have seen another page:

- [head](linux-basics-01.md#head-output-the-first-part-of-files)
- [tail](linux-basics-01.md#tail-output-the-last-part-of-files)

to look at the beginning or the end of file.

### Table melt and cast



![Working on it!](../tutorials/figs/constructocat.jpg)
