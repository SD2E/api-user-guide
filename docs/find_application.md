---
layout: page
title: Find an Application
tagline:
---

The SD2E catalog has several pre-built public applications (or "apps" for short) 
that are available to run right now. Each public application is tied to a public
HPC system.

<br>
#### List applications

The `apps-list` command can be used to list all applications you have access to:
```
% apps-list
kallisto-0.43.1u1
sailfish-0.10.1u1
```

This can be modified with `-P` or `-Q` flags to show only public or private apps,
respectively. Apps with a suffix of `u1, u2, u3...etc` are updates or revisions of
previous apps.

<br>
#### Search for applications by name

Use the `apps-search` command to see if an app you are interested in is available
in the SD2E catalog:
```
% apps-search -h    # show syntax for searching
% apps-search public=true name.like=*kallisto*
kallisto-0.43.1u1
```

In this case, the `apps-search` command finds one match and returns the newest
revision of the kallisto app.

<br>
#### Determine the application function

Many applications you will find in the catalog can be used in different ways.
For example, depending on how kallisto is called on the command line, it can be
used to build an index, quantify abundances of transcripts from RNA-Seq data,
run a pseudoalignment, or convert HDF5-formatted results to plain text. It is up
to the developer of the SD2E app to decide which function(s) this particular
instance of the app will perform, and to appropriately document it in the app
description.

To see the app description, perform:
```
% apps-list -v kallisto-0.43.1u1
```

The output of this command is a `json` description of the app including metadata,
inputs, parameters, and outputs.

<br>
#### The `metadata` section

The `metadata` section of the app json file includes information about the app
availability, runtime resources required, description, and much more:
```json
 "available": true,
 "checkpointable": false,
 "defaultMaxRunTime": "00:30:00",
 "defaultMemoryPerNode": 256,
 "defaultNodeCount": 1,
 "defaultProcessorsPerNode": 20,
 "defaultQueue": "normal",
 "deploymentPath": "/public-apps/kallisto-0.43.1u1.zip",
 "deploymentSystem": "data-sd2e-app-assets",
 "executionSystem": "hpc-tacc-maverick",
 "executionType": "HPC",
 "helpURI": "https://pachterlab.github.io/kallisto/",
 "icon": null,
 "id": "kallisto-0.43.1u1",
 "isPublic": true,
 "label": "Kallisto",
 "lastModified": "2017-09-29T11:38:43.000-05:00",
 "longDescription": "A program for quantifying abundances of transcripts from RNA-Seq data",
 "modules": [
     "load tacc-singularity"
 ],
 "name": "kallisto",
 "ontology": [
     "http://edamontology.org/operation_3800",
     "http://edamontology.org/topic_3053"
 ],
 "owner": "sd2eadm",
 "parallelism": "SERIAL",
 "revision": 1,
 "shortDescription": "Quantifies RNA-Seq transcripts",
 "tags": [
     "RNA-Seq",
     "Quantification",
     "Abundance"
 ],
 "templatePath": "kallisto.sh.template",
 "testPath": "kallisto-tester.sh",
 "uuid": "6351565997271150105-242ac115-0001-005",
 "version": "0.43.1"
```

Some key information in this section includes the identity of the HPC system
(`executionSystem`) on which the app runs, which is `hpc-tacc-maverick`. Also,
the `longDescription` suggests that the function of this particular app is to
quantify abundances of transcripts from RNA-Seq data.

<br>
#### The `inputs` section

The `inputs` section describe what input data are required for running this app:
```json
 "inputs": [
     {
         "details": {
             "argument": null,
             "description": "FASTA file of transcript sequences",
             "label": "transcripts",
             "repeatArgument": false,
             "showArgument": false
         },
         "id": "transcripts",
         "semantics": {
             "fileTypes": [
                 "raw-0"
             ],
             "maxCardinality": 1,
             "minCardinality": 1,
             "ontology": [
                 "http://edamontology.org/format_1929"
             ]
         },
         "value": {
             "default": "",
             "enquote": false,
             "order": 0,
             "required": true,
             "validator": null,
             "visible": true
         }
     },
     {
         "details": {
             "argument": null,
             "description": null,
             "label": "First reads file of a FASTQ pair (or single)",
             "repeatArgument": false,
             "showArgument": false
         },
         "id": "fastq1",
         "semantics": {
             "fileTypes": [
                 "raw-0"
             ],
             "maxCardinality": 1,
             "minCardinality": 1,
             "ontology": [
                 "http://edamontology.org/format_1930"
             ]
         },
         "value": {
             "default": "read1.fastq",
             "enquote": false,
             "order": 0,
             "required": true,
             "validator": null,
             "visible": true
         }
     },
     {
         "details": {
             "argument": null,
             "description": null,
             "label": "Second reads file of a FASTQ pair",
             "repeatArgument": false,
             "showArgument": false
         },
         "id": "fastq2",
         "semantics": {
             "fileTypes": [
                 "raw-0"
             ],
             "maxCardinality": 1,
             "minCardinality": 1,
             "ontology": [
                 "hhttp://edamontology.org/format_1930"
             ]
         },
         "value": {
             "default": "",
             "enquote": false,
             "order": 0,
             "required": true,
             "validator": null,
             "visible": true
         }
     }
 ],
```

It appears this app is expecting three files as inputs, identified by the `id`
field: `transcripts`, `fastq1`, and `fastq2`. Each has a short description, and
all three are required to run this app.

<br>
#### The `parameters` section

Next, inspect the `parameters` section of the app json file:
```json
 "parameters": [
     {
         "details": {
             "argument": "-b ",
             "description": "Number of bootstrap samples (default: 0)",
             "label": "bootstrap",
             "repeatArgument": false,
             "showArgument": true
         },
         "id": "bootstrap",
         "semantics": {
             "maxCardinality": 1,
             "minCardinality": 0,
             "ontology": [
                 "xs:integer"
             ]
         },
         "value": {
             "default": 0,
             "enquote": false,
             "order": 0,
             "required": false,
             "type": "number",
             "validator": "",
             "visible": true
         }
     },
     {
         "details": {
             "argument": "--seed=",
             "description": "Seed for the bootstrap sampling (default: 42)",
             "label": "seed",
             "repeatArgument": false,
             "showArgument": true
         },
         "id": "seed",
         "semantics": {
             "maxCardinality": 1,
             "minCardinality": 0,
             "ontology": [
                 "xs:integer"
             ]
         },
         "value": {
             "default": 42,
             "enquote": false,
             "order": 0,
             "required": false,
             "type": "number",
             "validator": "",
             "visible": true
         }
     },
     {
         "details": {
             "argument": "-o ",
             "description": "Output directory name",
             "label": "output",
             "repeatArgument": false,
             "showArgument": true
         },
         "id": "output",
         "semantics": {
             "maxCardinality": 1,
             "minCardinality": 1,
             "ontology": [
                 "xs:string"
             ]
         },
         "value": {
             "default": "output",
             "enquote": false,
             "order": 1,
             "required": true,
             "type": "string",
             "validator": "",
             "visible": true
         }
     }
 ],
```

It is also expecting three parameters, identified by the `id` field: `bootstrap`,
`seed`, and `output`. This time, only the `output` parameter is requried. The 
purpose and acceptable values for each parameter are provided in the description.

<br>
#### The `outputs` section

The last remaining section is the `outputs` section:  
```json
 "outputs": [
     {
         "details": {
             "description": null,
             "label": "Abundances tabular file"
         },
         "id": "abundance",
         "semantics": {
             "fileTypes": [],
             "maxCardinality": 1,
             "minCardinality": 1,
             "ontology": [
                 "http://edamontology.org/format_3475"
             ]
         },
         "value": {
             "default": "output/abundance.tsv",
             "order": 0,
             "validator": null
         }
     },
     {
         "details": {
             "description": null,
             "label": "Metadata about the Kallisto run"
         },
         "id": "run_info",
         "semantics": {
             "fileTypes": [],
             "maxCardinality": 1,
             "minCardinality": 1,
             "ontology": [
                 "http://edamontology.org/format_3464"
             ]
         },
         "value": {
             "default": "output/run_info.json",
             "order": 0,
             "validator": null
         }
     }
 ],
```
Here, we see two outputs are created when running this app: `abundance` and
`run_info`. Neither must be specified by the user, as they are output by default.
However, this is an important clue (along with the inputs, parameters, and app
description) to indicate what is the function of this app.

Once the appropriate app has been identified, the next steps are to 
[stage the input data](data_management.md), and to
[prepare and submit a job](submit_job).

---
Return to the [API Documentation Overview](../index.md)
