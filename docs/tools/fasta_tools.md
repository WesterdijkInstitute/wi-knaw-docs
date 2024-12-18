# FASTA-tools

> The code can be found at [https://www.github.com/b-brankovics/fasta_tools](https://www.github.com/b-brankovics/fasta_tools).

This package contains Perl programs/scripts that perform frequently
needed operations on FASTA format files. Such as adjusting the line
length to a uniform length, reverse complementing sequences,
identifying entries with identical sequences, etc.

The executable files are located in the [bin](bin) folder.

------------------------------------

## Programs

### [fasta_reversecomplement](bin/fasta_reversecomplement)

```text
Usage:
        fasta_reversecomplement [-h | --help] [FASTA file | -]

Description:
        A tool to reverse complement the entries of FASTA format files

Options:
        -h | --help
                Print the help message; ignore other arguments.
```

### [fasta_length](bin/fasta_length)

```text
Usage:  
        fasta_length [Options] [FASTA file | -]

Description:
        A tool to get sequence length information for FASTA files

Options:
        -h | --help
                Print the help message; ignore other arguments.
        -e
                Print the sequence length for each entry and the total length.
        -o
                Print the sequence length for each entry.
```

### [fasta_grep](bin/fasta_grep)

A tool to filter entries from a multi FASTA file
based on entry names using regular expression(s).
(Replaces `fasta_extract` and `fasta_remove`)

```text
   fasta_grep
    This is a script to filter entries from multi FASTA format.

   Synopsis
        fasta_grep ['<regex>'] [-v] [-min=<INT>] [-max=<INT>] [<input.fas>]...

   Options
    -h | --help Print the help message; ignore other arguments.

    -min=INT | --min=INT
                Filter out reads that are shorter than INT.

    -max=INT | --max=INT
                Filter out reads that are longer than INT.

    -or | --OR  There are three filter options: 1. REGEX 2. Minimum length
                3. Maximum length

                By default, an entry has to pass all specified filters to be
                considered matching (Boolean AND). Using this flag changes
                this setting so an entry is considered matching if it passes
                any of the filters (Boolean OR).

                In case of multiple regular expressions, if an entry matches
                any of them, then it passes the REGEX filter. If you need an
                entry to satisfy all regular expressions than combine them
                by piping multiple calls of this script.

    '<regex>'   A Perl style regular expression to be used for filtering. To
                use /cov=\d+/ for filtering, the following regex arguments
                can be used: "cov=\d+" | 'cov=\d+' | cov=\\d+

                Using single quotes is the most recomended, to avoid bash
                interpretting special characters before the argument is
                handed to perl. Multiple regular expression can be
                specified: an entry matching any of these is considered to
                be matching.

    -v | --invert-match
                Invert the sense of matching, to select non-matching
                entries.

    -tab=FILE | --tab=FILE
                Reads the search patterns from the file. It splits the lines
                by tabs and uses the first part as pattern and the second
                one to add as tag when printing the output.

    -e | --exact-id
                The pattern has to match the ID part of the def line in the
                FASTA. The same as using '/^>$regex\b/' on the def line.

   Input
    FASTA format file containing sequences that will be filtered. There can
    be multiple sequence files specified and the script can also read from
    STDIN. If no FASTA files are specified then it will automatically read
    from STDIN. Also if - is included as argument, then STDIN will be read.

    Files with the following extensions are recognized as FASTA: '*.fas' |
    '*.fa' | '*.fsa' | '*.fasta'

   Output
    FASTA format file is printed to STDOUT after filtering.

   Examples
    a) Extract RPB2 entries

        fasta_grep  RPB2  input.fas >output.fas
        fasta_grep 'RPB2' input.fas >output.fas
        fasta_grep "RPB2" input.fas >output.fas

    b) Filter incomplete sequences out

        fasta_grep -v  incomplete  input.fas >output.fas
        fasta_grep -v 'incomplete' input.fas >output.fas
        fasta_grep -v "incomplete" input.fas >output.fas

    c) Keep contigs that have at least 10X coverage (cov=<INT>)

        fasta_grep 'cov=\d{2,}'  input.fas >output.fas
        fasta_grep "cov=\d{2,}"  input.fas >output.fas
        fasta_grep cov=\\d\{2,\} input.fas >output.fas

    d) Extract RPB1 and RPB2 entries

        fasta_grep 'RPB[12]' input.fas >output.fas

    e) Filter RPB1 and RPB2 entries from input1.fas input2.fas and
    input3.fas

        fasta_grep 'RPB[12]' input1.fas input2.fas input3.fas >output.fas
        cat input1.fas | fasta_grep 'RPB[12]' - input2.fas input3.fas >output.fas
        cat input2.fas | fasta_grep 'RPB[12]' input1.fas - input3.fas >output.fas
        cat input3.fas | fasta_grep 'RPB[12]' input1.fas input2.fas - >output.fas

    f) Filter RPB1 entries and filtering incomplete entries out (requires
    two steps)

        cat input1.fas | fasta_grep 'RPB1' | fasta_grep -v 'incomplete' >output.fas
```

### [fasta_sort](bin/fasta_sort)

A tool to sort entries from a multi FASTA file
based on entry names using regular expression(s).

```text
   fasta_sort
    This is a script to sort entries in multi FASTA format.

   Synopsis
        fasta_sort ['<regex>'] [--rest] [<input.fas>]...

   Options
    -h | --help Print the help message; ignore other arguments.

    '<regex>'   A Perl style regular expression to be used for the sorting.
                To use /cov=(\d+)/ for sorting the following regex arguments
                can be used: "cov=(\d+)" | 'cov=(\d+)' | cov=\(\\d+\)

                Using single quotes is the most recomended, to avoid bash
                interpretting special characters before the argument is
                handed to perl. Actual sorting is done on the capture
                parenthesis (in /cov=(\d+)/ it is '(\d+)'), if no capture
                parenthesis is included then the regex is surrounded by
                parenthsis (e.g. 'cov=\d+' becomes '(cov=\d+)').

    -r | --rest The entries that are not matched by the regular expression
                will be sorted by an alphanumeric sort. If it is not used,
                then entries not matched by the regular expression will be
                kept in the original order.

   Input
    FASTA format file containing sequences that will be sorted. There can be
    multiple sequence files specified and the script can also read from
    STDIN. If no FASTA files are specified then it will automatically read
    from STDIN. Also if - is included as argument, then STDIN will be read.

   Output
    FASTA format file is printed to STDOUT after sorting.

   Examples
    a) Sort entries based on /cov=(\d+)/ (coverage value) and entries that
    are not matched are sorted alphanumerically

        fasta_sort 'cov=(\d+)' --rest input.fas >output.fas

    b) Sort entries based on /cov=(\d+)/ (coverage value) and entries that
    are not matched are kept in the original order

        fasta_sort 'cov=(\d+)' input.fas >output.fas

    c) Sort entries based on sequence name alphanumerically

        fasta_sort input.fas >output.fas

    d) Sort entries based on sequence name alphanumerically by reading from
    STDIN

        cat input.fas | fasta_sort >output.fas
        cat input.fas | fasta_sort - >output.fas

    e) Sort entries from input1.fas input2.fas and input3.fas as in b)

        fasta_sort 'cov=(\d+)' input1.fas input2.fas input3.fas >output.fas
        cat input1.fas | fasta_sort 'cov=(\d+)' - input2.fas input3.fas >output.fas
        cat input2.fas | fasta_sort 'cov=(\d+)' input1.fas - input3.fas >output.fas
        cat input3.fas | fasta_sort 'cov=(\d+)' input1.fas input2.fas - >output.fas
```

### [fasta_similarity](bin/fasta_similarity)

A tool to measure sequence similarity of aligned sequences in multi
FASTA format files.

```text
Usage:
        fasta_similarity [-h | --help] [FASTA file | -]

Description:
        A tool to measure sequence similarity of aligned sequences in multi FASTA format files

Options:
        -h | --help
                Print the help message; ignore other arguments.
```

### [fasta_variability](bin/fasta_variability)

A tool to measure sequence variability of aligned sequences in multi
FASTA format files.

```text
Usage:
    fasta_variability [-h | --help] [FASTA file | -]

Description:
    A tool to measure sequence variability of aligned sequences
    in multi FASTA format files

Options:
    -h | --help
        Print the help message; ignore other arguments.
```

### [fasta_sub](bin/fasta_sub)

A tool to extract a part of the sequences from FASTA files.

```text
Usage:
        fasta_sub [-h | --help] [FASTA file | -] (<int> <int> | <int>..<int>)

Description:
        A tool to extract a part of the sequences from FASTA files

Options:
        -h | --help
                Print the help message; ignore other arguments.
```

### [fasta_shift](bin/fasta_shift)

A tool to shift circular FASTA sequences using
a reference FASTA file or a position.

```text
Usage:  
        fasta_sub [-h | --help] [FASTA file | -] (<int> <int> | <int>..<int>)

Description:
        A tool to extract a part of the sequences from FASTA files

Options:
        -h | --help
                Print the help message; ignore other arguments.

(base) bbrankovics@DSK-MEYE-002:~$ fasta_shift -h
Usage:  
        fasta_shift -i (FASTA file)|-  -r (FASTA file)|-
        fasta_shift -i (FASTA file)|-  -p <int>

Description:
        A tool to shift circular FASTA sequences using
         a reference FASTA file or a position

Options:
        -h | --help
                Print the help message; ignore other arguments.
        -i | --input
                Specifies the input file to be used. '-' if input is STDIN
        -r | --ref
                Specifies the reference file to be used. '-' if input is STDIN
        -p | --pos
                Specifies the position of the sequence which should be the new start position.
                The position should be a positive integer. 1 is the start position of the original sequence.
```

### [fasta_find](bin/fasta_find)

A tool to reporting exact sequence matches of entries in a reference
FASTA file in a target FASTA file.

```text
Usage:
        fasta_find [-h | --help] <reference.fas> <target.fas>

Description:
        A tool to find exact matches of a reference fasta file in a target fasta file

Options:
        -h | --help
                Print the help message
```

Example:

```bash
fasta_find gene.fas chr.fas
```

### [fasta_unique](bin/fasta_unique)

A tool to remove duplicate sequence from FASTA format files and
print the groups to STDERR.

```text
Usage:
        fasta_unique [-h | --help] [FASTA file | -]

Description:
        A tool to remove duplicate sequence from FASTA format files and
         print the groups to STDERR

Options:
        -h | --help
                Print the help message; ignore other arguments.
        -t=<output> | --triple=<output>
        -t <output> | --triple <output>
                Print grouping information as tab-separeted triples to <output> file.
```

Example:

```bash
fasta_unique input.fas >unique.fas 2>unique.tab
```

### [fasta_deunique](bin/fasta_deunique)

A tool to format FASTA file to replace names from duplicate removal
by fasta_unique. Using the produced fasta file and the names table.

```text
Usage:
        fasta_deunique -i (FASTA file)|-  -tab (names table)

Description:
        A tool to format FASTA file to replace names from duplicate removal
         by fasta_unique. Using the produced fasta file and the names table.

Options:
        -h | --help
                Print the help message; ignore other arguments.
```

Example:

```bash
fasta_deunique -i unique.fas -tab unique.tab >deunique.fas
```

### [fasta_pretty](bin/fasta_pretty)

```text
Usage:
        fasta_pretty [-h | --help] [FASTA file | -]

Description:
        A tool to format fasta files to uniform column width (60)

Options:
        -h | --help
                Print the help message; ignore other arguments.
        -s | --single
                Print the sequence on a single line.
        -w=<int> | --width<int>
                Print the sequence using <int> width.
```

Examples:

Format a single file (`input.fas`) and save it to a file (`output.fas`)
can be done in any of the following way:

```bash
fasta_pretty input.fas >output.fas
cat input.fas | fasta_pretty >output.fas
cat input.fas | fasta_pretty - >output.fas
```

Format and concatenate three FASTA files from the current directory
(`input1.fas`, `input2.fas` and `input3.fas`) and save it to a file (`output.fas`).

```bash
fasta_pretty input1.fas input2.fas input3.fas >output.fas
fasta_pretty input*.fas >output.fas
cat input2.fas | fasta_pretty input1.fas - input3.fas >output.fas
```

### [fasta_dealign](bin/fasta_dealign)

A tool to format FASTA files to remove gap character states and format
to uniform column width (60).

```text
Usage:
        fasta_dealign [-h | --help] [FASTA file | -]

Description:
        A tool to format FASTA files to remove gap character states and format to uniform column width (60)

Options:
        -h | --help
                Print the help message; ignore other arguments.
```

Examples:

Format a single file (`input.fas`) and save it to a file
(`output.fas`).

```bash
fasta_dealign input.fas >output.fas
cat input.fas | fasta_dealign >output.fas
cat input.fas | fasta_dealign - >output.fas
```

Format and concatenate three FASTA files from the current directory
(`input1.fas`, `input2.fas` and `input3.fas`) and save it to a file
(`output.fas`).

```bash
fasta_dealign input1.fas input2.fas input3.fas >output.fas
fasta_dealign input*.fas >output.fas
cat input2.fas | fasta_dealign input1.fas - input3.fas >output.fas
```

### [fasta_assembly_statistics](bin/fasta_assembly_statistics)

A tool to calulate assembly statistics for FASTA files.

It calculates the following statistics:

- number of contigs
- total size (bp)
- N50 (bp)
- L50: smallest number of contigs whose length sum produces N50
- mean contig size (bp)
- longest contig (bp)
- third quartile (bp)
- median (bp)
- first quartile (bp)
- shortest contig (bp)
- number of Ns
- number of gaps (/N+/): number of N-stretches in the sequences
- number of other IUPACs: IUPAC bases are nucleotide ambiguity codes (YRWSKMDVHB)

```text
   fasta_assembly_statistics
    A tool to calulate assembly statistics for FASTA files.

   Synopsis
        fasta_assembly_statistics [OPTIONS] [FILE]...

   Options
    -r | --report
                Generate report for each file specified as input. The output
                format is a tab delimited (tsv) file with file name, key,
                value.

    -h | --help Print the help message; ignore other arguments.

   Input
    STDIN and/or FASTA files. The extention of the files is irrelevant.

   Output
    A set of statistic data about the assembly file. Each entry is assumed
    to be a contig of the assembly. This can be captured in a file by using
    the ">" or ">>" operator.

   Examples
    Calculate for a single file ("input.fas").

        fasta_assembly_statistics input.fas
        cat input.fas | fasta_assembly_statistics
        cat input.fas | fasta_assembly_statistics -
```

### [fasta_display_alignment](bin/fasta_display_alignment)

A tool to display sequence alignments in either pairwise or a multiple alignment
fashion.

```text
Usage:
        fasta_display_alignment [-h | --help] [-w=<int> | --width=<int>] [-p | --pairwise] [FASTA file | -]

Description:
        A tool to display sequence alignments in either pairwise or a multiple alignment fashion.

Options:
        -h | --help
                Print the help message; ignore other arguments.
        -w=<int> | --width=<int>
                Set the width of the sequence that will be displayed per line to <int>.
        -p | --pairwise
                Display alignments in pairs similarly to an exonerate output.
                At the start the sequence IDs are printed and some alignment statistics.
        -b | --block
                Display alignment in 'block format'. Consensus positions are shown with a '*'.
                At the start the sequence IDs are printed and at the end some alignment statistics.
                Each alignment chunck starts by the range displayed and ends with a line with position info.
                This is the default option.

```
