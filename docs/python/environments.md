# Python environments

There are two important concepts in managing python code environments: dependence management and virtual environments.

The tools listed here can be split in two groups: python and conda. Conda can manage python packages and environments, but also packages of other languages.

Besides installing tools and software through your operating systems installation system there are two common installation options that are provided for different (python) tools: `pip` and `conda`.

**Environment managers**

- `venv` (uses `pip`)
- `virtualenv` (uses `pip`)
- `pipenv`
- `conda`

**Dependency managers**

- `pip`
- `pipenv`
- `conda`

[Freecodecamp post on venv](https://www.freecodecamp.org/news/how-to-manage-python-dependencies-using-virtual-environments/)


[Best practices for conda and pip](https://www.anaconda.com/blog/using-pip-in-a-conda-environment)

- Use pip only after conda
    + install as many requirements as possible with conda, then use pip
    + pip should be run with --upgrade-strategy only-if-needed (the default)
    + Do not use pip with the --user argument, avoid all “users” installs
- Use conda environments for isolation
    + create a conda environment to isolate any changes pip makes
    + environments take up little space thanks to hard links
    + care should be taken to avoid running pip in the “root” environment
- Recreate the environment if changes are needed
    + once pip has been used conda will be unaware of the changes
    + to install additional conda packages it is best to recreate the environment
- Store conda and pip requirements in text files
    + package requirements can be passed to conda via the --file argument
    + pip accepts a list of Python packages with -r or --requirements
    + conda env will export or create environments based on a file with conda and pip requirements

I you have tools that are in different conda environments, then you can still use [snakemake to manage workflows](https://snakemake.readthedocs.io/en/stable/snakefiles/deployment.html).
