---
layout: page
title: Prepare and Submit a Job
tagline:
---

Continuing with the previous example of kallisto (see [Find an Application](find_application.md)),
we know there are three required input files, one required input parameter, and
two optional input parameters for this specific application.

<br>
#### Build a job template file

To run an instance of this application with our data (called a "job"), we first
must assemble a json description of the job we would like to run. The simplest
way to do this is to use the Agave `jobs-template` command:
```
% jobs-template kallisto-0.43.1u1

{
  "name": "kallisto test-1506708374",
  "appId": "kallisto-0.43.1u1",
  "archive": true,
  "inputs": {
    "transcripts": "read1.fastq",
    "fastq1": "",
    "fastq2": ""
  },
  "parameter": {
    "output": "output"
  }
}
```
By default, this is output to the screen. To store it in a file instead,
perform:
```
% jobs-template kallisto-0.43.1u1 > kallisto_job.json
```
We see very basic information including an identifying name for our job, the 
identity of the app, and whether it should be archived. Also, we see only the 
required inputs and parameters. (All optional inputs and parameters can be 
automatically supplied using the `-A` flag. See `jobs-template -h` for more
details).

<br>
#### Add input files to job template

If you staged your own data to your private STORAGE system, now is the time to
provide the path to your data. This is easy to do using Agave URIs. For example,
see these URIs describing the path to publicly accessible data for this 
kallisto job:
```
 "inputs": {
   "transcripts": "agave://data-sd2e-community/sample/kallisto/test/transcripts.fasta.gz",
   "fastq1": "agave://data-sd2e-community/sample/kallisto/test/reads_1.fastq.gz",
   "fastq2": "agave://data-sd2e-community/sample/kallisto/test/reads_2.fastq.gz"
 },
```

The prefix for an Agave URI is always `agave://` followed by the STORAGE system,
followed by the complete path relative to your root directory, and finally the name
of the file. Modify your `kallisto_job.json` file to point to either this public 
data, or your data that you have staged on your private STORAGE system.

<br>
#### Add parameters to job template

Initially only the required parameter `output` is listed. If we wish to use
non-default values for the unlisted parameters, `bootstrap` and `seed`, we must
add them to the job template now:
```
 "parameters": {
   "output": "output",
   "bootstrap": 100,
   "seed": 1
 }
```

The json format is very unforgiving with typos. If your job file is not accepted,
you may consider running it through an external [JSON validator](https://jsonlint.com/).

<br>
#### Submit a job

Once you are satisfied that your data is staged and the job template file contains
the instructions you want to use to run the job, use the `jobs-submit` command
to submit the job:
```
% jobs-submit -F kallisto_job.json
Successfully submitted job 833421020533756391-242ac11b-0001-007
```

If there are no errors, you will see a success message along with a long unique
identifier (UID) for your job. You can monitor the progress of the job with the `jobs-list`
command and, optionally, the job UID:

```
% jobs-list
833421020533756391-242ac11b-0001-007 PENDING
```

<br>
#### Download the results

Once the job status is `FINISHED`, you can list what output is available:

```
% jobs-output-list 833421020533756391-242ac11b-0001-007
.agave.archive
.agave.log
_util
app.yml
kallisto-job.json
kallisto-tester.sh
kallisto.json
kallisto.sh.template
kallisto_test-833421020533756391-242ac11b-0001-007.err
kallisto_test-833421020533756391-242ac11b-0001-007.out
kallisto_test.ipcexe
output
reads_1.fastq.gz
reads_2.fastq.gz
test
transcript.idx
transcripts.fasta.gz
```

The important output for this job is all located in the `output` directory.
Download just the `output` directory using the following command:
```
% jobs-output-get -r 833421020533756391-242ac11b-0001-007 /output/
Downloading output/abundance.h5 ...
######################################################################## 100.0%
Downloading output/abundance.tsv ...
######################################################################## 100.0%
Downloading output/run_info.json ...
######################################################################## 100.0%
```

Or, you can download all of the job files including output, logs, and other run
time files using the following command:
```
% jobs-output-get -r 833421020533756391-242ac11b-0001-007
```

---
Return to the [API Documentation Overview](../index.md)
