# MarkDown

## Intro

> You can find info on other formats at the [web formats page](web-formats.md)

MarkDown was developed by [John Gruber](https://daringfireball.net/projects/markdown/)
as an easy-to-read, easy-to-write plain text format for web writers.
It is a simple (but powerful) markup language for writting web content ([HTML](web-formats.md#html)).
The overriding design goal for Markdown’s formatting syntax was to make it as
readable as possible. The idea is that a Markdown-formatted document should be
publishable as-is, as plain text, without looking like it’s been _marked up_
with _tags_ or _formatting instructions_.

Example of a formated text:

```md
This _is_ a **paragraph**
```

That will look as follows:

This _is_ a **paragraph**

There are different "flavours" of MarkDown with slight differences in syntax
and with different added extra features (like adding images or tables):

- The original [MarkDown by John Gruber (Daring Fireball)](https://daringfireball.net/projects/markdown/)
- [Pandoc Markdown](http://pandoc.org/MANUAL.html#pandocs-markdown)
(or [according to RStudio](https://rmarkdown.rstudio.com/authoring_pandoc_markdown.html))
- [GitHub Markdown](https://guides.github.com/features/mastering-markdown/)
- [Rmarkdown](https://rmarkdown.rstudio.com/lesson-8.html) ([Cheatsheet](https://www.rstudio.com/wp-content/uploads/2016/03/rmarkdown-cheatsheet-2.0.pdf))
- [GitLab Markdown](https://about.gitlab.com/handbook/product/technical-writing/markdown-guide/)


## Advanced features or extensions

### Mermaid for flowcharts

- [Live (online) mermaid editor](https://mermaid.live/edit)
- [GitLab example with mermaid](https://about.gitlab.com/handbook/tools-and-tips/mermaid/)

### Math formulas

> Need to write this. $$ y = x^2-3 + log_2(e^4) $$

### Rmarkdown

R has a special format called [Rmarkdown](rmarkdown.md) (`.Rmd`),
which is an interesting extension of markdown.
You can create code blocks in Markdown and specify the different coding language which can tell
the formating tools which code highlighting to use. With Rmarkdown you can have R code blocks that are
executed when converting markdown to a display format (e.g. HTML or PDF). You can even have R bits in
your text that will be subsituted by their value.

