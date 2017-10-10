---
layout: page
title: Create Custom Applications
tagline: Add your app to the SD2E tenant
---

At this stage, most of the files are ready to go. Your directory structure
should be similar to:
```
fastqc/
├── Dockerfile                 # done !
├── build.sh                   # done !
├── deploy.sh
├── example                    # done !
│   ├── SP1.fq                 # done !
│   ├── SP1_fastqc.html        # done ! (can remove)
│   └── SP1_fastqc.zip         # done ! (can remove)
├── fastqc-0.11.5
│   ├── _util                  # done ! (did not change)
│   │   └── container_exec.sh  # done ! (did not change)
│   ├── app.json
│   ├── app.yml
│   ├── job.json
│   ├── runner-template.sh     # done !
│   └── tester.sh              # done !
└── src                        # done !
    ├── README.md              # done !
    └── fastqc_v0.11.5.zip     # done ! (optional)
```
The final step required before you can run the app is to edit the `json` file
descriptions so your app is compatible with Agave.

<br>
#### Write the app description

The Agave app description is contained in `app.json`. All `app.json` files
consist of four essential sections: metadata, input, parameters, and output.
```
{	"application_metadata":"value",
	"inputs":[],
	"parameters":[],
	"outputs":[]
}
```

The metadata section will contain information about our app name, version, where
and how an instance of the app should run, the location of the app bundle, and
more. There is only one input required: the name (and location) of the fastq file
to be processed. We do not need to specify any parameters or outputs at this time.

A full list of all metdata, inputs, parameters, and outputs conventions can be
found in the [Agave documentation](http://developer.agaveapi.co/#apps). However,
rather than try to write the `app.json` from scratch, we will modify the template
to contain the following:
```
{
  "name": "username-fastqc",
  "version": "0.11.5",
  "executionType": "HPC",
  "executionSystem": "hpc-tacc-maverick-username",
  "parallelism": "SERIAL",
  "deploymentPath": "/ghiban/applications/agave/fastx/stampede",
  "deploymentSystem": "data.iplantcollaborative.org",
  "defaultProcessorsPerNode": 1,
  "defaultNodeCount": 1,
  "defaultQueue": "normal",
  "label": "FastQC",
  "modules": [ "load tacc-singularity/2.3.1" ],
  "shortDescription": "A Quality Control application for FastQ files",
  "templatePath": "runner-template.sh",
  "testPath": "tester.sh",
  "inputs": [
    {
      "id": "fastq",
      "value": {
        "default": "",
        "visible": true,
        "required": true
      },
      "details": {
        "label": "FASTQ sequence file",
        "showArgument": false,
      },
      "semantics": {
        "minCardinality": 1,
        "maxCardinality": 1,
        "ontology": [ "http://edamontology.org/format_1930" ]
      }
    }
  ],
  "parameters": [],
  "outputs": []
}
```

Some of the more useful fields are shown above. We use our private execution
system (`hpc-tacc-maverick-username`) to run this app. The `deploymentPath` and
`deploymentSystem` fields show where the app assets (including the wrapper 
script) reside. The `templatePath` and `testPath` fields should match the names
of the files we created. And, as this is a simple `SERIAL`, `HPC` job, it will
be submitted to the `normal` queue using `1` processor

The `inputs` section only contains one input - the `fastq` file to be processed.
Note the `id` of the input paramter, `fastq`, matches the environment variable
in our `runner_template.sh` script. 

The JSON format can be tricky, so it is a good idea to validate your JSON using
an [online tool](https://jsonlint.com/) to make sure there are no missing 
characters.

The YAML file (`app.yml`) is probably not strictly necessary. It is a clone of
the `app.json` file, just in a different format. You may perform the
conversion using [online tools](https://www.json2yaml.com/).


<br>
#### Write a job template

Instructions for running an instance of an app (a job), are located in the 
`job.json` file. Here is an example template for our job:
```
{
  "name": "Unique-job-name",
  "appId": "username-fastqc-0.11.5",
  "archive": false,
  "inputs": {
    "fastq": "agave://data-sd2e-community/sample/kallisto/test/reads_2.fastq.gz"
  }
}
```

Give the job a unique name, the `appId` is a concatenation of the app `name` and
`version` from the `app.json` file above, the `archive` flag can be `true` or
`false`, and the only input required is a `fastq` file


<br>
#### Inspect and run the deploy script

The delploy script does two things. It stages your app assets (including the 
wrapper script) on the storage system specified in the app description, and it
creates a new app in the SD2E tenant using the Agave `apps-addupdate` command.
```
% bash deploy.sh
```


<br>
#### Run a test job

Now that your app and assets are staged, the final step is to run a test job.
```
% jobs-submit -F job.json
```

---
Proceed to [Next steps](create_application_05.md)

Go back to [Create Custom Applications](create_application.md)

Return to the [API Documentation Overview](../index.md)
