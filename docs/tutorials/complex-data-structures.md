# Using complex data structures

## Formats

Common formats used for storing data and frequently used
to pass information between websites and tools:

- [JSON](../formats/web-formats.md#json)
- [XML](../formats/web-formats.md#xml)
- [YAML](../formats/web-formats.md#yaml)

Linked data formats:
- JSON-LD: JSON linked data
- RDF
- Turtle
- OWL

## Tools

Coverting between the three main formats using `yq`.
It is a YAML tool, but since JSON is valid YAML syntax, both
formats are perfect as input.

```bash
# convert YAML to JSON
cat <YAML file> | yq e -o j - 
yq e -o j <YAML file>

# convert YAML to XML
cat <YAML file> | yq e -o x - 
yq e -o x <YAML file>

# convert XML to YAML
cat <XML file> | yq e -p x -
yq e -p x <XML file>

# convert JSON to simple YAML
cat <JSON file> | yq e -o x - | yq e -p x -
```

Since JSON is valid YAML to force a simplified YAML format that is no longer valid JSON,
we need to first convert it to XML and then to YAML.

Example data:

```json
{
  "DocumentSummarySet": {
    "+status": "OK",
    "DbBuild": "Build221130-2030.1",
    "DocumentSummary": [
      {
        "Id": "9788391",
        "RsUid": "26177968",
        "GbUid": "26143128",
        "AssemblyAccession": "GCF_017869345.1",
        "Taxid": "34020",
        "Organism": "Candidatus Liberibacter africanus (a-proteobacteria)",
        "SpeciesTaxid": "34020",
        "SpeciesName": "Candidatus Liberibacter africanus",
        "BioSampleAccn": "SAMN15222106",
        "BioSampleId": "15222106",
        "Biosource": {
          "Isolate": "Ang37"
        }
      },
      {
        "Id": "346721",
        "RsUid": "1799558",
        "GbUid": "1799048",
        "AssemblyAccession": "GCF_001021085.1",
        "Taxid": "1277257",
        "Organism": "Candidatus Liberibacter africanus PTSAPSY (a-proteobacteria)",
        "SpeciesTaxid": "34020",
        "SpeciesName": "Candidatus Liberibacter africanus",
        "BioSampleAccn": "SAMN03763724",
        "Biosource": {
          "InfraspeciesList": {
            "Infraspecie": {
              "Sub_type": "strain",
              "Sub_value": "PTSAPSY"
            }
          }
        }
      }
    ]
  }
}
```

This a part of the result produced by `esearch -db assembly -query "Liberibacter africanus[Organism:exp]" | esummary` in JSON format.

### Entrez direct: xtract

> [Entrez direct documentation](https://www.ncbi.nlm.nih.gov/books/NBK179288/) is found at NCBI.

The `xtract` tool is really powerful to extract info from XML format.
In the XML file returned by `esummary`, each entry is within a `DocumentSummary` tag
so we could ask xtract to extract the right values for each entry. The only problem is that if
a value is missing instead of inserting an empty of NA value it just skips the column for that entry.
This means that if the value is absent in some but not all then they will have different column counts,
and you don't know which value belongs to which column.

```
cat xml | xtract -pattern DocumentSummary -element AssemblyAccession Isolate BioSampleAccn
```
This produces a nice TSV output:
```
GCF_017869345.1 SAMN15222106
GCF_001021085.1 SAMN03763724
```


The results can get weird when you don't have exactly one element per pattern,
because all of the matches will be returned and blank columns won't be added
if there is no element within a given pattern. This way columns can get shifted in the output.

```
cat xml | xtract -pattern DocumentSummary -element AssemblyAccession Isolate BioSampleAccn
```
Will produce this:
```
GCF_017869345.1 Ang37   SAMN15222106
GCF_001021085.1 SAMN03763724
```

If select a pattern one level higher then there are multiple elemnts with the same name
```
cat xml | xtract -pattern DocumentSummarySet -element AssemblyAccession Isolate BioSampleAccn
```

Will produce a single row

```
GCF_017869345.1 GCF_001021085.1 Ang37   SAMN15222106    SAMN03763724
```

### jq

> [Documentation](https://stedolan.github.io/jq/manual/) and [playground](https://jqplay.org/)
>
> Other useful resources: [a blog post](https://earthly.dev/blog/jq-select/), [cheetsheet](https://gist.github.com/olih/f7437fb6962fb3ee9fe95bda8d2c8fa4)

jq is a lightweight and flexible command-line JSON processor. 

Prep step is to convert the xml to JSON.

```
cat xml | yq e -p x -o j >json
```

To get the same results as with xtract but in a proper layout we need to do this:

```
cat json | jq -r '.DocumentSummarySet.DocumentSummary[] | [.AssemblyAccession, .Biosource.Isolate, .BioSampleAccn] | @tsv'
```

This will produce 2 rows with 3 columns each. The second row will not have a value in the Isolate column, which what we would expect.

```
GCF_017869345.1	Ang37	SAMN15222106
GCF_001021085.1		SAMN03763724
```

An alternative way of getting the same result, but not specifying the complete path of nodes, is the following:

```
cat json | jq -r '.. | objects | select(.AssemblyAccession) | [.AssemblyAccession, .Biosource.Isolate, .BioSampleAccn] | @tsv'
```

Here `.. | objects` itterates through all the objects (`{}`), and we only select the the ones that have `.AssemblyAccession` fields.

### Perl scripting

Convert TSV to JSON. If the first line of the TSV is a header line starting with a tab.

```
cat tsv | perl -ne 'BEGIN{use JSON}; s/\R//g; if ($. == 1) {($dump, @col) = split/\t/;} else {$i= 0; ($id, @t) = split/\t/; $a{"id"} = $id; for(@t) {$a{$col[$i]} = $_; $i++}; push @json, \%a}; END{print encode_json(\@json)}' | jq -S '.'
```


## Linked data (FAIR) formats:

```json
{
  "@context": {
    "givenName": "http://schema.org/givenName",
    "familyName": "http://schema.org/familyName"
  },
  "@id": "http://me.markus-lanthaler.com/",
  "@type": "http://schema.org/Person",
  "givenName": "Markus",
  "familyName": "Lanthaler",

}
```
