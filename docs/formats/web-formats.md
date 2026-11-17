# Web formats

- [HTML](#html)
- [MarkDown (md)](#markdown)
- [CSS](#css)
- [XML](#xml)
- [JSON (Javascript Object Notation)](#json)
- [YAML](#yaml)

## HTML
HTML stands for **H**yper **T**ext **M**arkup **L**anguage. HyperText
is a text that is linked to other text via (hyper)links.
Markup languages are document types where you not only
see the _text/content_, but also the _formatting_ of the content. The text
is _marked up_ with formatting. Examples of markup languages are HTML,
[MarkDown](#markdown) and LaTeX. Editors and formats where
you don't see the formatting itself only the content and the result of
the formatting are called WYSIWYG (**W**hat **Y**ou **S**ee **I**s
**W**hat **Y**ou **G**et), most well known example is MS Word.
![hypertext image from Wikipedia](https://upload.wikimedia.org/wikipedia/commons/4/41/Sistema_hipertextual.jpg)

Exemple for a complete HTML doucment (indentation is not necessary, but makes it easier to read):
```html
<!DOCTYPE html>
<html>
  <head>
    <title>Page Title</title>
  </head>
  <body>
    <h1>This is a Heading</h1>
    <p>This is a paragraph.</p>
  </body>
</html>
```

Example of a formated text:

```html
This <em>is</em> a <b>paragraph</b>.
```

That will look as follows:

This _is_ a **paragraph**

Resources:

- [W3Schools HTML Tutorial](https://www.w3schools.com/html/default.asp)

## MarkDown

MarkDown was developed by [John Gruber](https://daringfireball.net/projects/markdown/)
as an easy-to-read, easy-to-write plain text format for web writers.
It is a simple (but powerful) markup language for writting web content ([HTML](#html)).
The overriding design goal for Markdown’s formatting syntax was to make it as
readable as possible. The idea is that a Markdown-formatted document should be
publishable as-is, as plain text, without looking like it’s been _marked up_
with _tags_ or _formatting instructions_.

There are different "flavours" of MarkDown with slight differences in syntax
and with different added extra features (like adding images or tables):

- The original [MarkDown by John Gruber (Daring Fireball)](https://daringfireball.net/projects/markdown/)
- [Pandoc Markdown](http://pandoc.org/MANUAL.html#pandocs-markdown)
(or [according to RStudio](https://rmarkdown.rstudio.com/authoring_pandoc_markdown.html))
- [GitHub Markdown](https://guides.github.com/features/mastering-markdown/)
- [Rmarkdown](https://rmarkdown.rstudio.com/lesson-8.html) ([Cheatsheet](https://www.rstudio.com/wp-content/uploads/2016/03/rmarkdown-cheatsheet-2.0.pdf))
- [GitLab Markdown](https://about.gitlab.com/handbook/product/technical-writing/markdown-guide/)


Example of a formated text:

```md
This _is_ a **paragraph**
```

That will look as follows:

This _is_ a **paragraph**

The markdown to produce the example [HTML](#html) code from above would be the following:

```md
# This is a Heading
This is a paragraph.
```

Here we don't specify the title. which in some cases can be done by using a [YAML](#yaml) header.


```md
---
title: Page title
---
# This is a Heading
This is a paragraph.
```

## CSS

**C**ascading **S**tyle **S**heets (CSS) is a style sheet language
used for describing the presentation of a document written in a markup language such as [HTML](#html).

To put it simply, CSS tells programs what the layout and style of the document should be.
This is why the internet has colors and not just white pages with black text and blue links :).
HTML defines the content and the formating/structure (paragraph or header, etc), but CSS
gives the instructions how that should be visualized (e.g. header 1 is 16pt, bold and in green).

You can learn more about it at [W3 school](https://www.w3schools.com/css/).

## XML

XML is short for E**x**tensible **M**arkup **L**anguage.
XML looks similar to HTML, but it is used for data storage and transfer.

```xml
<note>
  <to>Tove</to>
  <from>Jani</from>
  <heading>Reminder</heading>
  <body>Don't forget me this weekend!</body>
</note>
```

The examples is from [W3School](https://www.w3schools.com/xml/xml_whatis.asp) which is
a good resource if you want to learn more about XML.

## JSON
JSON stands for **J**avascript **O**bject **N**otation.

Using the same example that we had for XML would look like this in JSON:

```json
{"note":{"to": "Tove", "from": "Jani", "heading": "Reminder", "body": "Don't forget me this weekend!"}}
```
Using the indented layout:

```json
{
  "note": {
    "to": "Tove",
    "from": "Jani",
    "heading": "Reminder",
    "body": "Don't forget me this weekend!"
  }
}
```

To expand JSON into the indented format you can use some common command line tools:

```bash
# Using python
python -m json.tool <JSON file>
# Using Perl
cat <JSON file> | json_pp
# JQ is a powerful tool for JSON
jq . <JSON file>
cat <JSON file> | jq
# You can also sort the JSON key-value pairs
cat <JSON file> | jq -S
```

Resources:

- [W3Schools JSON Tutorial](https://www.w3schools.com/js/js_json_intro.asp)

## YAML
[Officially](https://yaml.org/) YAML stands for YAML Ain't Markup Language.

> Other sources say: **Y**et **A**nother **M**arkup **L**anguage. :D

YAML is a simple format that is surprizing hard to fully understand.
According to [official sources](https://github.com/yaml/yaml-grammar):
"_Fully comprehending the YAML grammar is quite an undertaking for most mortals.
Creating a fully compliant parser has proven almost impossible._"

> From [wikipedia](https://en.wikipedia.org/wiki/YAML):
> "_YAML is a human-readable data-serialization language. 
> It is commonly used for configuration files and in applications 
> where data is being stored or transmitted. 
> YAML targets many of the same communications applications as 
> Extensible Markup Language ([XML](#xml)) but has a minimal syntax.
> It uses Python-style indentation to indicate nesting,
> and a more compact format that uses [...] for lists and {...} for maps
> thus JSON files are valid YAML 1.2._"

Most of the times, you will encounter them as configuration files or
headers in documents (kind of an internal config file).
A good example is the [Rmarkdown format](https://rmarkdown.rstudio.com/lesson-8.html):

```yaml
---
title: "Untitled"
author: "Anonymus"
date: "4/5/2022"
output: html_document
---
```

Here you can see that that several key value pairs are used to set some variables.

Another example is a config file used for Snakemake in PPS NGS:

```yaml
samples:
  103165-001-006:
    lib:
      L001:
        R1: reads/103165-001-006_L001_R1.fastq.gz
        R2: reads/103165-001-006_L001_R2.fastq.gz
      L003:
        R1: reads/103165-001-006_L003_R1.fastq.gz
        R2: reads/103165-001-006_L003_R2.fastq.gz
  103165-033-011:
    lib:
      L001:
        R1: reads/103165-033-011_L001_R1.fastq.gz
        R2: reads/103165-033-011_L001_R2.fastq.gz
```

Here you can see a nested structure of key-value pairs.

Also using the XML example translated into YAML:

```yaml
note:
  to: "Tove"
  from: "Jani"
  heading: "Reminder"
  body: "Don't forget me this weekend!"
```

Since YAML is way easier to type than JSON, you can also create a YAML file that
you then convert to JSON using a tool like [yq](https://github.com/mikefarah/yq).

```bash
# convert YAML to JSON
cat <YAML file> | yq e -o j - 
yq e -o j <YAML file>
# convert JSON to YAML
cat <JSON file> | yq e -P -
yq e -P <JSON file>
# convert YAML to XML
cat <YAML file> | yq e -o x - 
yq e -o x <YAML file>
```
