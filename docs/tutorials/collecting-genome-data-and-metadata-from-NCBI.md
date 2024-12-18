# Collecting genome data and metadata from NCBI

For this example we use assemblies from NCBI of _Liberibacter_ spp.

> We will mostly use NCBI's `datasets` and `dataformat` commands that
> can be installed using conda `conda install conda-forge::ncbi-datasets-cli`

```bash
datasets download genome taxon Liberibacter --dehydrated --exclude-genomic-cds --exclude-gff3  --exclude-protein --exclude-rna --reference --filename liberi-genome.zip
unzip liberi-genome.zip -d dehydrated
```

## Exploring JSON files

```bash
datasets summary genome taxon Liberibacter  >liberi.json
```

This creates a large and complex JSON file as output that looks confusing when viewed using `cat` or `less`.
To make it easier to understand let's convert it to a more human friendly formatting with indented spacing,
and sort keys alphanumerically.

```bash
cat liberi.json | jq '.' -S | less
```

That is easier to read, but the ammount of information can be still overwhelming.

Let's see what are the top level keys in the file.

```bash
cat liberi.json | jq '. |= keys'
```

Okay, we have assemblies and `total`. (In this case total is the number of assemblies in the file. You can check its value with `cat liberi.json | jq '.total'`.)
We are interested in assembly information. Let's see what is the next layer of information.

> If you look at the top part of the complete JSON. `cat liberi.json | jq '.' -S | head`:
> 
>     {
>       "assemblies": [
>         {
>           "assembly": {
>             "annotation_metadata": {
>               "file": [
>                 {
>                   "estimated_size": "99674",
>                   "type": "GENOME_GFF"
>                 },
> 
> We can see that the JSON file consists of an object (starts with `{` followed by a key value pair `"assemblies": []`).
> The value of assemblies is an array (list; it starts with `[`) that contains "assembly" objects (`{"assembly":{}}`.)

Let's get the top level keys within the assembly objects (inside `assemblies`).

```
cat liberi.json | jq '.assemblies[] | . |= map_values(keys)' | less
```

```
{
  "assembly": [
    "annotation_metadata",
    "assembly_accession",
    "assembly_level",
    "bioproject_lineages",
    "biosample",
    "biosample_accession",
    "chromosomes",
    "contig_n50",
    "display_name",
    "estimated_size",
    "gc_count",
    "org",
    "paired_assembly_accession",
    "seq_length",
    "submission_date",
    "submitter"
  ]
}
```

You can explore the different elements by creating an array per entry (assembly).

```
cat liberi.json | jq  '.assemblies[] | .assembly | [.assembly_accession, .seq_length, .org, .assembly_category]' | less
```

We want to get the assembly ID, sequence length, organism name and ID, and whether it is a representative genome or not. You will see what the structure of `org` is, so let's update that part.
Plus modify the command to generate TSV output (by finishing the inner pipe with `@tsv`, and adding `-r`.)

```
cat liberi.json | jq  '.assemblies[] | .assembly | [.assembly_accession, .seq_length, .org.sci_name, .org.tax_id, .assembly_category] | @tsv ' -r | less
```

We can see that not all species have a representative genome (at least on 2023-06-05.).
If you want to classify genomes based on ANI scores against reference genomes, then
you need to select a genome yourself for these species.


## Download the genomes for the given taxon

Here we want to download the genome sequences, but we don't need the other types for formats.

```bash
datasets download genome taxon Liberibacter --filename liberi-genomes.zip
```

We can explore the metadata similarly, but because it has a different inner structure we will use NCBI's tool `ncbi_dataformat`.

```bash
#dataformat tsv genome --fields assminfo-accession,assminfo-biosample-accession,assmstats-total-sequence-len,organism-name,tax-id,isolate,strain,assminfo-refseq-category --package liberi-genomes.zip
dataformat tsv genome --fields accession,assminfo-biosample-accession,assmstats-total-sequence-len,organism-name,organism-tax-id,organism-infraspecific-isolate,organism-infraspecific-strain,assminfo-refseq-category --package liberi-genomes.zip
```

Here, I included `assminfo-biosample-accession`, so we can see that some samples have two genomes in the set (one RefSeq [GCF] and one non-Refseq [GCA]).

> Step ideas:
> 
> 1. Create TSV file for overview and documentation
> 2. Select GCF for the ones that have both
> 3. Save new file that is filtered
> 4. Move assembly files for the selected one to a new folder (e.g. genomes), and rename to ID.fna instead of the ugly name
>     - `mv liberi-genomes/ncbi_dataset/data/$id/$id*.fna genomes/$id\.fna`
> 5. update oveview file with absolute paths
> 6. Clean-up: delete download files and folders

## Looking up genome metadata

- using [`datasets summary`](#exploring-json-files)
- using [assembly info for an NCBI dataset](#download-the-genomes-for-the-given-taxon)
- using [entrez tools (esearch and esummary)](complex-data-structures.md)

Let's see how we can use ENTREZ on the command line and how it compares to NCBI's website.

> [Official documentation](https://www.ncbi.nlm.nih.gov/books/NBK25499/))

### Look up based on taxon name or ID

```
esearch -db assembly -query "Liberibacter[Organism:exp]" | esummary >liberi.xml
```

Here we are running a search in the assembly DB by typing in [`Liberibacter[Organism:exp]`](https://www.ncbi.nlm.nih.gov/assembly/?term=Liberibacter%5BOrganism%3Aexp%5D).

> For quick comparison you can generate URLs for the browser using this formula:
> `https://www.ncbi.nlm.nih.gov/$db/?term=$query`
> in our case that is `https://www.ncbi.nlm.nih.gov/assembly/?term=Liberibacter[Organism:exp]`.
> The difference between the URL that you might see in your browser is due to conversion of
> special characters to HTML code (e.g. `%5B` is `:`)

This query produces an XML output that you can convert to JSON ([see](complex-data-structures.md#jq), so you can use the tools from above. 

```
cat liberi.xml | yq e -p x -o j >liberi.json
```

### Lookup based on ID

Since you already know the ID for which you want to get more info, you don't need to search,
so we don't need `esearch` only `esummary`.

```
esummary -id GCF_000023765.2,GCF_001543305.1 -db assembly >summary.xml
```

When you want to use multiple IDs, separate them by commas (`,`).

## Looking up biosample metadata

You can use the methods above to access the biosample info via the assembly, but
if you want to get an overview of a specific or a set of IDs, then you can use
esummary in the same way as for assemblies.

Biosample contains the information on where the sample was taken and strain information,
so this basically what you need for publication overview tables most often.

```
esummary -id SAMN02603311,SAMN02604277 -db biosample
```

