# Starting with READMEs

A **README** is a text file that introduces and explains a project or folder.
It contains information that is commonly required to understand what the
project is about or what the different contents of the folder are.
It may link the specific content into a bigger context by offering links
to different related content and resources.

> You can also think about a README as the **metadata** for the given folder
> (be it on your computer, shared drive, OneDrive or in a git repository).

It is common practice to use **[MarkDown](../formats/markdown.md)** for
writing your READMEs. MarkDown is a simple markup language that

- is easy to learn,
- can be read easily (even if you don't know the formating rules, and without
  clogging the text up too much),
- is standard format that can be converted to many other formats
  (most importantly to HTML),
- and if you place README.md in your git repo online
  (e.g. [GitHub](https://github.com/b-brankovics/fasta_tools)) it becomes
  automatically rendered (converted) into HTML and displayed on the page.

> Markup languages are document formats where you can se the formating
> instructions. This is in contrast to formats like Word documents that
> belong to the WYSWYG formats (**W**hat **Y**ou **S**ee is **W**hat
**Y**ou **G**et), where you don't see the formating instructions only their results.

## Editing and viewing MarkDown files

You can use any kind of text editor to get edit and create markdown files with.
If you work on a Windows computer you can create your first MarkDown using
_Notepad_. If you want to check how it looks when it is converted to HTML,
then save it to your OneDrive and open it through your browser
([online OneDrive](https://hubrecht-my.sharepoint.com/)) or through MS Teams.

> If you use a git website ([GitHub](https://github.com),
> [GitLab](https://gitlab.com) or [BitBucket](https://bitbucket.org))
> then you can use the website to edit your MarkDown files and you have
> the option to check the preview while editing. After committing then it
> will display the rendered version of your makrdown.

If you want to go more towards being a pro use **[Visual Studio Code][Code]** or
**[RStudio][RStudio]**.

[Code]: https://code.visualstudio.com/
[RStudio]: https://www.rstudio.com/

[Visual Studio Code][Code] is a versatile text editor.

- You can load folders, so it is displayed in a similar fashion as Windows browser
  displayes folder structures.
- You can use **Markdown Preview** to see a live view of how your `.md` file
  would be rendered into HTML.
- **Pros**
    - It is fast and offers a live view
    - Easy to navigate in your folders
    - It can even help you use git version control
- **Cons**
    - You need to install extra extensions to get the "pros" functionalities
    - Difficult to use it for RMarkDown

[RStudio][RStudio] is an integrated development environment (IDE) for
the R programming language. RStudio is a great way to get introduced to using
MarkDown to create other document types like presentations or reports
(mostly in PDF and HTML).

- Userfriendly
    - many functionalities are already installed
    - when you need an update it will let you know and makes it easy to install it
- Has many templates to get started with
- Offers integration of R and markdown or HTML
- **Pros**
    - You can create markdown files through the File menu
    - You can click on Preview (`Ctrl` + `Shift` + `k`) to convert markdown to HTML
    - By default saves an HTML version of your document
    - Native suport for Rmakrdown
- **Cons**
    - There is no live view for MarkDown
    - It takes a surprisingly long time to convert markdown to HTML (it uses R
      and pandoc, which is not that fast especially on a Windwos)

In addition, you can also install extensions for your browsers so that they can
preview markdown files for you.

## Basic syntax and creating your first README

You can follow along [this online tutorial](https://www.markdowntutorial.com)
and/or check out the resources listed on the [MarkDown page](../formats/markdown.md).

## Documenting analysis steps

> This is a first draft for this template

```markdown
# Analysis title

> Description of the experiment or analysis.

> The goals of the experiment:
> - goal 1
> - goal 2

## Samples or input

> Describe what is used for the analysis, and its source

## Software environment

> - Describe where the analysis is executed (e.g. moya, CLC workbench, WSL on
>   own laptop).
> - Mention if a conda environment is used (bonus point if you can provide an
>   environment.yaml to reproduce the environment), or other such tool (e.g. docker)
> - Version of the tools used

## Used software

> Which programs are used, and where can they be found (e.g. github)
>
> This section can be combined with the previous one

## Logbook

> Note what was done, and note observations similar to lab experiments.

## Results

> Describe the results and result files (and where are the result files stored).

## Conclusions

> Are there conclusions like recommendations or biological findings?
>
> Note follow ups as well.
>
> Mention what could be improved in the future for similar research questions.

```

This can be used within a git project. Other sources like PPTs and eLAB can point
to the url of the git project containing the markdown file.

This markdown could also be zipped together with the necessary result files and
saved for documentation (protocol has to be discussed).

The file can be also saved in eLAB, or in a project folder on the W-drive.

> **Later, the guidelines for using READMEs will be added here.**
>
> ![Working on it!](figs/constructocat.jpg)
> For the moment _still under construction_.
