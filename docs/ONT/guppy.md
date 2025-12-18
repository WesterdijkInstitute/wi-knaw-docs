# Basecalling and debarcoding of MinIon (ONT) data

Methods:

- On the MinKNOW
- On a (windows) laptop
- On a server

> Some methods recommended to me a few years ago:
>
> - minionQC (R script) https://github.com/roblanf/minion_qc
> - Nanoplot (python) https://github.com/wdecoster/NanoPlot
> - assemblies from metagenomic samples: https://nanoporetech.com/resource-centre/using-long-nanopore-reads-assemble-genomes-complex-metagenomic-samples”


## Guppy: Oxford Nanopore's basecaller program

List of commands:

- guppy_aligner
- guppy_barcoder
- guppy_basecall_server
- guppy_basecall_client
- guppy_basecaller
- guppy_basecaller_duplex
- guppy_basecaller_supervisor

Other commands in the folder:

- autoconfigure_guppy_server.py 
- bam_convert
- crashpad_handler
- minimap2

## dorado

> source: [https://github.com/nanoporetech/dorado](https://github.com/nanoporetech/dorado)

## Basecalling minION data on moya with Guppy

Basecalling of minION data using CPUs is possible with Guppy.

```text
Usage:

With config file:
  guppy_basecaller -i <input path> -s <save path> -c <config file> [options]
With flowcell and kit name:
  guppy_basecaller -i <input path> -s <save path> --flowcell <flowcell name>
    --kit <kit name>
List supported flowcells and kits:
  guppy_basecaller --print_workflows
```

You can choose how many cores to use with two parameters:
     `--num_callers`
     `--cpu_threads_per_caller`

The sum of both is the amount of threads that are used in parallel to do the basecalling. In the example below that means 4 callers with 4 threads each is a total of 16 CPU threads running at the same time.
It seems that activating more callers with less CPUs does take a bit longer in the starting process than activating less callers with more CPUs each (needs to be confirmed). The runtime seems similar.

```bash
guppy_basecaller --input_path #test --save_path #test --flowcell FLO-FLG001 --kit SQK-LSK109 --num_callers 4 --cpu_threads_per_caller 4 --do_read_splitting
```

Overall runtime of basecalling is dependent on the size of the datasets and probably also the length of the reads. (add some information on benchmarking here maybe)

## Debarcoding MinIon data with guppy

Debarcoding can be done during basecalling with the parameter `–barcode_kits <kit name>`. However, no further options can be chosen for demultiplexing. To have other options available you need to do debarcoding after basecalling.

```text
Usage:

guppy_barcoder --input_path <folder containing FASTQ and/or FASTA files> --save_path <output folder> --config configuration.cfg --barcode_kits <kit name>
```

The configuration file is included in the installation. However, you can make your own configuration file.

The guppy barcoder has many options, which can be found with

or here https://community.nanoporetech.com/docs/prepare/library_prep_protocols/Guppy-protocol/v/gpb_2003_v1_revaf_14dec2018/barcoding-demultiplexing

The most frequently used option will likely be:

-   `--require_barcodes_both_ends` will only classify reads with barcodes on both ends
-   `--detect_mid_strand_barcodes` will not classify reads with a barcode in the middle since that is indicative of chimeras
-   `--min_score_barcode_front` minimum score for barcode detection. The default is 60. Lower values will allow for less stringent barcode detection and possibly more reads that are unclassified, while higher values will be more stringent and result in less classified reads. This option sets the score for both front and rear barcode if not defined otherwise

It is usually recommended to use the first two options when not using a rapid barcoding kit.
