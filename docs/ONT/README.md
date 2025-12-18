# ONT data processing

- [Basecalling and debarcoding](guppy.md)
- [Assembly](#assembling-reads)
- [Polishing](#polishing)
- [Read mapping](#read-mapping)
- [Detection](#detection)

## Oxford Nanopore resources

- [Download software](https://community.nanoporetech.com/downloads)
- [How to install Guppy](https://community.nanoporetech.com/docs/prepare/library_prep_protocols/Guppy-protocol/v/gpb_2003_v1_revae_14dec2018/linux-guppy)

## Assembling reads

According to one [Oxford Nanopore tutorial](https://labs.epi2me.io/notebooks/Assembly_Tutorial.html)
the basic recommendation is to use [flye](#flye)
for small to mid-sized genomes, and meta-genomic applications.
The other two assemblers that are recommended are [canu](#canu) and [shasta](#shasta).
[Canu](#canu) is generally considered the best but requires tuning the estimated genome size
and has an extensive runtime.
[Shasta](#shasta) is much faster and should provide reliable results.
[Trycycler](https://github.com/rrwick/Trycycler) requires a lot of third party tools as it relies on making consensus assemblies with a number of assemblers (you choose which ones). It is not possible to automate, as it requires manual inspection of clusters. It should be able to eliminate biases of the different assemblers.

Assemblers:

- [flye](#flye) (with `--meta` flag for viruses and metagenomes)
- [shasta](#shasta)
- [Canu](#canu)
- [RATTLE](#rattle): simple assembler using clustering and consensus generation
- [miniasm](#miniasm): simple, but frequently not useful
- [Trycycler](https://github.com/rrwick/Trycycler/wiki): is not a stand alone assembler, but wrapper for a number of tools resulting in an assembly pipeline for bacterial genomes. Tedious to install and use, but with very good results and intermediate visualizations.
        -used with [flye](#flye), [canu](#canu), [miniasm and minipolish](#https://github.com/rrwick/Minipolish/blob/main/miniasm_and_minipolish.sh), [NECAT](https://github.com/xiaochuanle/NECAT) and [raven](https://github.com/lbcb-sci/raven)

Based on personal experience (Balazs) [flye](#flye),[shasta](#shasta) and [RATTLE](#rattle)
appear to be most useful in most cases, because of the shorter runtime and
they appear to be less "selective" in their assembly: in the case of viruses
it is more likely that you will get assembly of host sequences.
[Canu](#canu) seems to be tricky for virus assemblies. If expected genome size is not
set properly (wish I could tell you what that is), then you may end up with only
one contig even if the virus has more than 1 segments with high coverage.

Both [flye](#flye) and [Canu](#canu) produce long, good quality contigs when the underlying
reads are good enough. Other assemblers may yield more fragmented assemblies with more
sequence variants for different regions. In addition, [Canu](#canu) can produce good assemblies
even when the reads contain palindrom duplications. Palindrom duplication confound the [flye](#flye)
assemblies, and result in short contigs that have a palindrom structure.

**Conclusions:**

- Start with a [flye](#flye) (with `--meta` flag) assembly
- If you want to obtain good quality sequences, try running [Canu](#canu) with a genome size estimate based on the [flye](#flye) assembly
   + You can play around with the settings until you get better results than [flye](#flye) or progress with the [flye](#flye) assembly
- ONT assemblies may contain several SNPs that may be corrected by polishing (see [Polishing](#polishing) section).


**Apart from miniasm I (Balazs) would recommend all of the other assemblers. Although canu has a significantly higher runtime than the others and is less consistent in length.**


### Flye

> Source: [https://github.com/fenderglass/Flye](https://github.com/fenderglass/Flye)

**Important:** For virus samples, you need to use the `--meta` flag, otherwise you will get an empty assembly.

```bash
flye --meta --nano-raw reads.fastq --out-dir flye-assembly --threads 20
```

It is slower than shasta, but it looks promising.

### Canu

> Source: [https://github.com/marbl/canu](https://github.com/marbl/canu)

```bash
canu -d run1 -p canu-assembly genomeSize=50k -nanopore-raw reads.fq.gz useGrid=false
```

> One some systems, you have to add `useGrid=false`, otherwise it may fail.

It produces a clear assembly with consensus sequences.
Sensitive to the genome size setting which can have a large effect on which contigs are assembled or not.

### shasta

> Source: [https://github.com/chanzuckerberg/shasta](https://github.com/chanzuckerberg/shasta)

Shasta filters the input reads based on length. The default setting is 10000bp long. Usually we need to lower that threshold.

```bash
shasta --input reads.fq --config Nanopore-projectX --Reads.minReadLength=500 --assemblyDirectory shasta-assembly
```

> One one system, only sudo had rights to run `--memoryBacking 2M --memoryMode filesystem`
> which is what shasta recommends when you use the default settings.
>
> ```bash
> shasta --input reads.fq --config Nanopore-projectX --Reads.minReadLength=500 --assemblyDirectory shasta-assembly  --memoryBacking 2M --memoryMode filesystem
> shasta --command cleanupBinaryData
> ```


### miniasm

> Source: [https://github.com/lh3/miniasm](https://github.com/lh3/miniasm)

Should be a simple assembly tool that is based on overlap analysis without error correction.
It usually does not work for the samples. Might work for small genomes that have a high
coverage.

### RATTLE

> Source: [https://github.com/comprna/RATTLE](https://github.com/comprna/RATTLE)

RATTLE is designed to assemble transcriptomes.
In addition, it may be used for polishing assemblies (not tested by us, yet).

It consists of the following steps:

1. `cluster` cluster reads (creates `clusters.out`)
2. `correct` consensus generation for the clusters (creates `consensi.fq`)
3. `polish` polish consensus sequences and build final transcriptome (creates `transcriptome.fq`)

It produces an assembly, but keeps a large amount of variation in the data. (This is obvious when comparing it with canu.)

### Trycycler

> Source: [https://github.com/rrwick/Trycycler/wiki](https://github.com/rrwick/Trycycler/wiki)

Trycycler is a tool that takes as input multiple separate long-read assemblies of the same genome (e.g. from different assemblers or different read subsets) and produces a consensus long-read assembly.

In brief, Trycycler does the following:

- Clusters the contig sequences, so the user can distinguish complete contigs (i.e. those that correspond to an entire replicon) from spurious and/or incomplete contigs.
- Reconciles the alternative contig sequences with each other and repairs circularisation issues.
- Performs a multiple sequence alignment (MSA) of the alternative sequences.
- Constructs a consensus sequence from the MSA by choosing between variants where the sequences differ.


## Polishing

Polishing is a post-assembly step that can improve the quality of the sequences.
It (re)maps reads to the contigs and generates consensus sequences.

Tools:

- [medaka](#medaka)
- [RATTLE](#rattle)
- [Polypolish](https://github.com/rrwick/Polypolish)

[Pilon](https://github.com/broadinstitute/pilon) is also a classical polishing tool developed by the Broad Institute.

### Medaka

> Source: [https://github.com/nanoporetech/medaka](https://github.com/nanoporetech/medaka)

[Medaka](https://github.com/nanoporetech/medaka) is useful for further improving good quality assemblies.

Example usage:

```bash
# Run the polishing by specifying basic details
medaka_consensus -d assembly.fasta -i input.fastq -o medaka -t 8 -m r941_min_high_g344
```

The polished output may contain less contings than the original assembly.

**Conclusion about polishing:**

- [medaka](https://github.com/nanoporetech/medaka) is useful for further improving good quality assemblies
- it is not recommended for detection pipelines, because it might remove low coverage sequences
- careful comparison between input and output of medaka is recommended

### Polypolish (for polishing with short reads)

> Source: [https://github.com/rrwick/Polypolish](https://github.com/rrwick/Polypolish)

Polypolish is a tool for polishing genome assemblies with short reads. Unlike other tools in this category, Polypolish uses SAM files where each read has been aligned to all possible locations (not just a single best location). This allows it to repair errors in repeat regions that other alignment-based polishers cannot fix.

## Read mapping

### Minimap2

> Source: [https://github.com/lh3/minimap2](https://github.com/lh3/minimap2)

## Detection

