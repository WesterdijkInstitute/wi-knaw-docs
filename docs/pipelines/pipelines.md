# Pipelines

The major pipeline tools:

- [Snakemake](https://snakemake.readthedocs.io/en/stable/)
- [Nextflow](https://www.nextflow.io/)
- [Common Workflow Language (CWL)](https://www.commonwl.org/)

## Pipeline ideas

Modules:

- Read prep before assembly per type
- Assembly
- PCR and TaqMan test (lastal)
- Annotation
- Extract homolog
- pangenomics & clustering

Check for inspiration:

- [Bactopia](https://journals.asm.org/doi/10.1128/msystems.00190-20)
- [MLSA and ANI pipeline (snakemake-mlsa-ani)](https://github.com/westerdijk-wm/snakemake-mlsa-ani)
- [Snakemake workflow: smkwf-bwa-gatk-snpeff](https://github.com/westerdijk-wm/smkwf-bwa-gatk-snpeff)

## Nextflow

> [Docs](https://www.nextflow.io/docs/latest/index.html)

- Get started (from the docs)
    - Get an [overview](https://nextflow.io/docs/latest/overview.html#overview-page)
      of Nextflow and its key concepts.
    - Get started with Nextflow by [installing it](https://nextflow.io/docs/latest/install.html#install-page)
      and running [your first script](https://nextflow.io/docs/latest/your-first-script.html#your-first-script).
    - Check out [this blog post](https://www.nextflow.io/blog/2023/learn-nextflow-in-2023.html)
      for even more resources on how to learn Nextflow.
- [Basic Nextflow Community Training](https://training.nextflow.io/basic_training/)
- [nf-core pipeline dev](https://training.nextflow.io/nf_develop/)
