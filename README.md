# WI-KNAW Docs

Setting up conda environment to use mkdocs:

```bash
conda env create -f mkdocs-env.yml
```

To generate a static website (collection of HTML files):

```bash
conda activate mkdocs
mkdocs build
```

To generate a dynamic website on localhost (updates when files are changed):


```bash
conda activate mkdocs
mkdocs serve
```
