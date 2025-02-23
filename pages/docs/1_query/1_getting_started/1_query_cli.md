---
title: 'Querying from the command line'
description: 'Execute SPARQL queries directly from the command line.'
---

The default Comunica query engine that exposes most standard features is Comunica SPARQL,
which uses the package name `@comunica/actor-init-sparql`.
In this guide, we will install it _globally_, and show how it can be invoked from the command line.

<div class="note">
This guide discusses querying over Linked Data on the Web, exposed via URLs.
<a href="/docs/query/getting_started/query_cli_file/">Click here to query over local RDF files</a> instead.
</div>

## 1. Installation

Since Comunica runs on Node.js, make sure you have [Node.js installed](https://nodejs.org/en/) on your machine.

Next, we can install Comunica SPARQL on our machine:
```bash
$ npm install -g @comunica/actor-init-sparql
```

## 2. SPARQL querying over one source

After installing Comunica SPARQL, you will be given access to several commands including `comunica-sparql`,
which allows you to execute SPARQL queries from the command line.

This command requires one or more URLs to be provided as **sources** to query over.
As last argument, as **SPARQL query string** can be provided.

For example, the following query retrieves the first 100 triples from [DBpedia](https://fragments.dbpedia.org/2016-04/en):
```bash
$ comunica-sparql https://fragments.dbpedia.org/2016-04/en \
    "SELECT * WHERE { ?s ?p ?o } LIMIT 100"
```

<div class="note">
Given a URL, Comunica will automatically detect the <a href="/docs/query/advanced/source_types/">type of source</a> and handle it accordingly.
</div>

As output, a JSON array of bindings for the selected variables will be returned:
```
[
{"?s":"https://fragments.dbpedia.org/2016-04/en#dataset","?p":"http://www.w3.org/1999/02/22-rdf-syntax-ns#type","?o":"http://rdfs.org/ns/void#datasource"},
{"?s":"https://fragments.dbpedia.org/2016-04/en#dataset","?p":"http://www.w3.org/1999/02/22-rdf-syntax-ns#type","?o":"http://www.w3.org/ns/hydra/core#Collection"},
{"?s":"https://fragments.dbpedia.org/2016-04/en#dataset","?p":"http://www.w3.org/ns/hydra/core#search","?o":"https://fragments.dbpedia.org/2016-04/en#triplePattern"}
...
``` 

## 3. Query file input

Since SPARQL queries can sometimes become very large, it is possible to supply them via a local file using the `-f` option.

Assuming a file `path/myquery.sparql` exists, we can query over it as follows:

```bash
$ comunica-sparql https://fragments.dbpedia.org/2016-04/en -f path/myquery.sparql
```

## 4. SPARQL querying over multiple sources

One key feature of Comunica is its ability to query over **multiple sources**.
For this, you can just supply any number of URLs as arguments.
Just make sure that the last argument remains your query.

```bash
$ comunica-sparql https://fragments.dbpedia.org/2016-04/en \
    https://www.rubensworks.net/ \
    https://ruben.verborgh.org/profile/ \
    "SELECT * WHERE { ?s ?p ?o } LIMIT 100"
```

## 5. SPARQL CONSTRUCT and ASK

Next to SPARQL `SELECT` queries,
it is also possible to execute `CONSTRUCT` queries to produce RDF triples:
```bash
$ comunica-sparql https://fragments.dbpedia.org/2016-04/en \
    "CONSTRUCT WHERE { ?s ?p ?o } LIMIT 100"
```
```text
<http://0-access.newspaperarchive.com.topcat.switchinc.org/Viewer.aspx?img=8716084> <http://dbpedia.org/property/accessdate> "2010-04-21"^^<http://www.w3.org/2001/XMLSchema#date>;
    <http://dbpedia.org/property/date> "1939-01-02"^^<http://www.w3.org/2001/XMLSchema#date>;
    <http://dbpedia.org/property/format> "PDF";
    <http://dbpedia.org/property/isCitedBy> <http://dbpedia.org/resource/List_of_Attorneys_General_of_Wisconsin>;
    <http://dbpedia.org/property/location> "Sheboygan, Wisconsin";
    <http://dbpedia.org/property/page> "1";
...
```

`ASK` queries will produce a boolean output:
```bash
$ comunica-sparql https://fragments.dbpedia.org/2016-04/en \
    "ASK { ?s ?p ?o }"
```
```
true
```

## 6. Changing result format

`SELECT` queries will be printed as JSON by default, and `CONSTRUCT` queries as [RDF TriG](https://www.w3.org/TR/trig/).
This can be overridden using the `-t` option.

For example, displaying results as SPARQL JSON results:
```bash
$ comunica-sparql https://fragments.dbpedia.org/2016-04/en \
    "SELECT * WHERE { ?s ?p ?o } LIMIT 100" \
    -t 'application/sparql-results+json'
```
```json
{"head": {"vars":["s","p","o"]},
"results": { "bindings": [
{"s":{"value":"http://0-access.newspaperarchive.com.lib.utep.edu/us/mississippi/biloxi/biloxi-daily-herald/1899/05-06/page-6?tag=tierce+wine&rtserp=tags/tierce-wine?page=2","type":"uri"},"p":{"value":"http://dbpedia.org/property/date","type":"uri"},"o":{"value":"1899-05-06","type":"literal","datatype":"http://www.w3.org/2001/XMLSchema#date"}},
{"s":{"value":"http://0-access.newspaperarchive.com.lib.utep.edu/us/mississippi/biloxi/biloxi-daily-herald/1899/05-06/page-6?tag=tierce+wine&rtserp=tags/tierce-wine?page=2","type":"uri"},"p":{"value":"http://dbpedia.org/property/isCitedBy","type":"uri"},"o":{"value":"http://dbpedia.org/resource/Tierce_(unit)","type":"uri"}},
{"s":{"value":"http://0-access.newspaperarchive.com.lib.utep.edu/us/mississippi/biloxi/biloxi-daily-herald/1899/05-06/page-6?tag=tierce+wine&rtserp=tags/tierce-wine?page=2","type":"uri"},"p":{"value":"http://dbpedia.org/property/newspaper","type":"uri"},"o":{"value":"Biloxi Daily Herald","type":"literal"}},
{"s":{"value":"http://0-access.newspaperarchive.com.lib.utep.edu/us/mississippi/biloxi/biloxi-daily-herald/1899/05-06/page-6?tag=tierce+wine&rtserp=tags/tierce-wine?page=2","type":"uri"},"p":{"value":"http://dbpedia.org/property/page","type":"uri"},"o":{"value":"6","type":"literal"}},
{"s":{"value":"http://0-access.newspaperarchive.com.lib.utep.edu/us/mississippi/biloxi/biloxi-daily-herald/1899/05-06/page-6?tag=tierce+wine&rtserp=tags/tierce-wine?page=2","type":"uri"},"p":{"value":"http://dbpedia.org/property/title","type":"uri"},"o":{"value":"A New System of Weights and Measures","type":"literal"}},
{"s":{"value":"http://0-access.newspaperarchive.com.lib.utep.edu/us/mississippi/biloxi/biloxi-daily-herald/1899/05-06/page-6?tag=tierce+wine&rtserp=tags/tierce-wine?page=2","type":"uri"},"p":{"value":"http://dbpedia.org/property/url","type":"uri"},"o":{"value":"http://0-access.newspaperarchive.com.lib.utep.edu/us/mississippi/biloxi/biloxi-daily-herald/1899/05-06/page-6?tag=tierce+wine&rtserp=tags/tierce-wine?page=2","type":"uri"}},
...
``` 

<div class="note">
All <a href="/docs/query/advanced/result_formats/">available formats</a> can be printed via <code>comunica-sparql --listformats</code>
</div>

## 7. Learn more

This guide only discussed the basic functionality of `comunica-sparql`.
You can learn more options by invoking the _help_ command:
```text
$ comunica-sparql --help

Usage:
  comunica-sparql http://fragments.dbpedia.org/2016-04/en [-q] 'SELECT * WHERE { ?s ?p ?o }'
  comunica-sparql http://fragments.dbpedia.org/2016-04/en [-q] '{ hero { name friends { name } } }' -i graphql
  comunica-sparql http://fragments.dbpedia.org/2016-04/en [-f] query.sparql
  comunica-sparql http://fragments.dbpedia.org/2016-04/en https://query.wikidata.org/sparql ...
  comunica-sparql hypermedia@http://fragments.dbpedia.org/2016-04/en sparql@https://query.wikidata.org/sparql ...

Options:
  -q            evaluate the given SPARQL query string
  -f            evaluate the SPARQL query in the given file
  --to          the destination for update queries
  -c            use the given JSON configuration file (e.g., config.json)
  -t            the MIME type of the output (e.g., application/json)
  -i            the query input format (e.g., graphql, defaults to sparql)
  -b            base IRI for the query (e.g., http://example.org/)
  -l            sets the log level (e.g., debug, info, warn, ... defaults to warn)
  -d            sets a datetime for querying Memento-enabled archives
  -p            delegates all HTTP traffic through the given proxy (e.g. http://myproxy.org/?uri=)
  --lenient     if failing requests and parsing errors should be logged instead of causing a hard crash
  --help        print this help message
  --listformats prints the supported MIME types
  --version     prints version information
```
