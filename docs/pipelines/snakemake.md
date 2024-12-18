# What is Snakemake?

> [Official Snakemake documation](https://snakemake.readthedocs.io/en/stable/)

Snakemake is workflow management system, a tool to create reproducible and scalable data analyses.
Workflows are described via a human readable, Python based language.
They can be seamlessly scaled to server, cluster, grid and cloud environments, without the need to modify the workflow definition.
Finally, Snakemake workflows can entail a description of required software, which will be automatically deployed to any execution environment.

## How can you use Snakemake?

Use cases:  

- You can write up an existing workflow
- You can write up a specific analysis for reproducibility
- You can build the Snakemake file (workflow) while you are analyzing your data

It can be handy to use Snakemake to explore or analyze your data, because then you
will have a reproducible analysis. It will be also clear what kind of steps you have taken.
Snakemake can check if intermediary results have changed as you optimize different steps.

## Some notes on using Snakemake

This is a very short overview of a few options based on the documentations and trial and error.

### Input

Input can be a single file, a list of files.
This can be explicitly stated or using a function.



Referencing input. Inputs can be done as list `{input}`
by pasting all input names separated by space.
It can be referencing to a given element `{input[0]}`.
You can reference them by name if they are named:



### Wildcards

Wildcards are generated based on (input and) output file names.

They can be referenced simply in `input`, `log` & `params` sections,
but in `shell` section they have to be accessed through the `wildcards`
variable. Wildcards can be also used by functions that define input
files, but they have to be passed as input variables.
See the example below:

```
def get_ref(wildcards):
    return config["samples"][wildcards.ref]

rule nucmer:
    input:
        get_ref,
	lambda wildcards: config["samples"][wildcards.query]
    output:
        "mummer/{ref}-{query}.delta"
    log:
        "logs/nucmer/{ref}-{query}.log"
    shell:
        "nucmer -maxmatch {input} -p mummer/{wildcards.ref}-{wildcards.query} > {log}"
```

Input functions can be `lambda` functions or named functions.

A function may return:  

1. A single string
2. A list of strings
3. A dictionary with named inputs, **but you need to `unpack()` it**

```
# 1. single string from a "named" function
def get_ref(wildcards):
    return config["samples"][wildcards.ref]

rule nucmer:
    input:
        get_ref(),
        "{query}.fas"
    output:
        "mummer/{ref}-{query}.delta"
    log:
        "logs/nucmer/{ref}-{query}.log"
    params:
        prefix="-p mummer/{ref}-{query}"
    shell:
        "nucmer -maxmatch {input} {params} > {log}"

# 2. A list of strings using an unnamed (lambda) function
rule overview:
    input:
        lambda wildcards: [ "{}.fas".format(sample) for config["samples"] ]
    output:
        "overview/assembly-stats.tsv"
    shell:
        "fasta_assembly_statistics -r {input}"


# 3. A dictionary is returned by this named function
def get_core_input(wildcards):
    return {'sizes': "genomes/{}.sizes".format(config["groups"][wildcards.group]["ref"]),
    	    'beds': ["flat/{}-{}.bed".format(config["groups"][wildcards.group]["ref"], q) for q in config["groups"][wildcards.group]["all"]]}


# Note unpack is needed here
rule get_core:
    input: unpack(get_core_input)
    output:
        "core-{group}.bed"
    params:
        total=lambda wildcards: len(config["groups"][wildcards.group]["all"])
    shell:
        "cat {input.beds} | sort -k1,1 | "
	"bedtools genomecov -bg -split -i - -g {input.sizes} | "
        "grep -P  \"^\\S*\\t\\S*\\t\\S*\\t{params.total}\\b\" | "
	"cut -f1-3 > {output}"
```


### Generator rule

Frequently you want to run the same workflow for many samples/files.
For this you can add a generator rule that has all the files that you
want to generate as its input, so this request will trigger all the
needed steps.

```
rule all:
    input:
        lambda wildcards: ["{}/{}{}{}".format(wildcards.dir, wildcards.pre, sample, wildcards.post) for sample in wildcards.set.split(',') ]
    output:
        "done:{dir}:{pre}:{set}:{post}"
```

If you have `samples = [ A, B, C ]`, and you want to compare them to a reference sequence (`best.fas`) using nucmer for which you have a rule with `mummer/{ref}-{query}.delta` as output.
You need to do the following:

```
snakemake -c 3 done:mummer:best-:A,B,C:.delta
```

> This will request the following files as input:
>
> - `mummer/best-A.delta`
> - `mummer/best-B.delta`
> - `mummer/best-C.delta`

### Dealing with ambiguity

As you can see from the generator rule, you can make complex rules
with parameters coded in the file names. But as your workflow grows,
it becomes harder for snakemake to guess which rules are needed to
produce the desired file. One thing that can help to make things clear
is to use specific extentions or different folders for different parts
of the workflow.

### Building modular workflows

Sometimes you want to have a general flow, but want to replace certain steps
with alternatives in some cases. An example could be detection pipeline that
uses reads to generate assemblies that are further analyzed. In this example,
you may need different assemblers based on the input data (Illumina, ONT, PacBio).

#### Using a master snakefile

Below is an example where the rules are defined in `source-snakefile` and they are loaded
into `Snakefile` based on the config file.

The example workflow consists of 3 steps, but there are two versions for step2
(step2a and step2b).

```
rule step2a:
    input:
        "output/step01.txt"
    output:
        "output/step02.txt"
    shell:
        """
        cat {input} > {output}
        echo "step 2 (this is A)" >> {output}
        """

rule step2b:
    input:
        "output/step01.txt"
    output:
        "output/step02.txt"
    shell:
        """
        cat {input} > {output}
        echo "step 2 (this is B-side)" >> {output}
        """

```

The config file specifies which will be used for the given workflow:

```
step2: A  # options['A', 'B']
```

Here is the control flow for selecting the right version:

```
configfile: "config.yaml"

module master:
    snakefile: "source-snakefile"


use rule step1 from master as step1

if config["step2"] == "A":
    use rule step2a from master as step2a
else:
    use rule step2b from master as step2b

use rule step3 from master as step3
```

#### Consistent structure

When designing a modular workflow it is very important
to have a clear plan for folder structures and output naming.
Think of the outputs as crucial nodes or checkpoints, you
need to make sure that alternative route you plan starts
from the same checkpoint and ends with the checkpoint as
the other route.

There is one more important consideration,
you have to be careful not to introduce extra ambiguity in
file names, as they are the core of the flow. One way to do
this is to separate steps and their alternatives by using
the same folder for the alternative steps (in the above example
having a separate folder for the steps and step2 a & b use the same folder.)

Here is an example of folder structure of a modular pipeline:

```
workflow
|-- config.yaml
|-- Snakefile
|-- db/
|   |-- {db}.fas
|   |-- {db}.tax
|   |-- {db}.trlp
|   |-- host.fas
|   \-- host.tax
|-- host/
|   |-- *.fas
|   \-- *.taxmap
|-- reads/
|-- assemblies/
|   |-- {sample}/
|   \-- {sample}_contigs.fas
|-- raw-sam/
|   \-- {sample}-{db}.sam
|-- filtered-sam/
|   |-- {sample}-{db}.sam
|   |-- {sample}-host.sam
|   \-- {sample}-host.ids
\-- report/
    |-- annotated-{sample}-{db}_ref.tsv
    |-- raw-{sample}-{db}_pair.tsv
    |-- raw-{sample}-{db}_ref.tsv
    |-- {sample}-assembly.tsv
    |-- {sample}-{db}_hits.tsv
    |-- {sample}-{db}.html
    \-- {sample}-{db}-viral.fas
```