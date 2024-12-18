# Environment management and containers

Tools:

- conda
- docker
- apptainer

## Python environments

## Conda

> [Conda docs](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html)

```bash
# List conda environments
conda env list
# Create a new env
conda create --name <my-env>
# Create based on environment file
conda env create -f env.yml
# Activate an existing environment
conda activate <my-env>
# install packages within an envirnment
conda install ...
# remove an envirnoment
conda remove -n <my-env> --all
# Move an environment to a new location
conda rename -p <current-path> <new-path>
```

Example environment YAML:

```yaml
name: ani-typer
channels:
  - conda-forge
  - bioconda
  - defaults
dependencies:
  # some tools
  - pyani
  - fastani
  - ncbi-datasets-cli
  # R 
  - r-base=4.3
  - r-dendextend
  - r-phylogram
  - r-phangorn
  - r-circlize
  - bioconductor-complexheatmap
  - r-viridis
  # Python
  - python=3.10
  - pandas
  - pip
  - pip:
    - docx
    - gooey
    - matplotlib==2.0.0 
```

## Apptainer

> [Docs](https://apptainer.org/docs/user/latest/quick_start.html)

Basic commands for getting started:

```bash
# Pull container from Docker and create a local apptainer image: lolcow_latest.sif
apptainer pull docker://ghcr.io/apptainer/lolcow
# Use the shell of the image
apptainer shell lolcow_latest.sif
# Run a command inside the image
apptainer exec lolcow_latest.sif cowsay moo
# Run a container (execute its runscript)
apptainer run lolcow_latest.sif
```

> ```bash
> apptainer build pps-ngs.sif docker://docker-registry.wur.nl/brank001/sam-harmonization:latest
> ```

### Building images from scratch

Apptainer produces immutable images in the Singularity Image File (SIF) format.
This ensures reproducible and verifiable images and allows for many extra benefits
such as the ability to sign and verify your containers.

However, during testing and debugging you may want an image format that is writable.
This way you can `shell` into the image and install software and dependencies until
you are satisfied that your container will fulfill your needs. For these scenarios,
Apptainer also supports the `sandbox` format (which is really just a directory).

These are the options to create apptainer images (SIFs):

- `pull` from another container (e.g. docker)
- `build --sandbox`
- `build` from one image to another (e.g. from a sandboxed version)
- `build` from a definition file
