---
layout: page
title: Create Custom Applications
tagline: Build wrapper and test scripts
---

If you have been following along, your fastqc app directory should contain:
```
fastqc/
├── Dockerfile                 # done !
├── build.sh
├── deploy.sh
├── example                    # done !
│   ├── SP1.fq                 # done !
│   ├── SP1_fastqc.html        # done ! (can remove)
│   └── SP1_fastqc.zip         # done ! (can remove)
├── fastqc-0.11.5
│   ├── _util
│   │   └── container_exec.sh
│   ├── app.json
│   ├── app.yml
│   ├── job.json
│   ├── runner-template.sh
│   └── tester.sh
└── src                        # done !
    ├── README.md              # done !
    └── fastqc_v0.11.5.zip     # done ! (optional)
```

<br> 
#### Write the wrapper script

The file loacted at `~fastqc/fastqc-0.11.6/runner-template.sh` contains runtime
instructions for your app. The current file is the template left over from the
hello-container example. Edit it as follows:
```
CONTAINER_IMAGE="index.docker.io/USERNAME/fastqc:0.11.5"

. _util/container_exec.sh

COMMAND='fastqc'
PARAMS='-o ${output_dir} ${fastq}'

container_exec ${CONTAINER_IMAGE} ${COMMAND} ${PARAMS}
```

Replace `USERNAME` with your Docker ID (or project space where the image is
located). In the above file, the `container_exec.sh` script is sourced, `fastqc`
is the command to execute, and the parameters passed to the command are `-o
${output_dir} ${fastq}`. Notice they are still variables. This will be resolved
later by Agave. The final command in the script is the container run command.
It is written in this way to be flexible and compatible to both Docker and
Singularity.

<br>
#### Write the tester script

The tester script is your opportunity to develop a robust app using best
practices. Edit it as follows:
```
#!/usr/bin/env bash

output_dir='${PWD}/../example'
fastq='${PWD}/../example/SP1.fq'

CONTAINER_IMAGE="index.docker.io/USERNAME/fastqc:0.11.5"

. _util/container_exec.sh

COMMAND='fastqc'
PARAMS='-o ${output_dir} ${fastq}'

DEBUG=1 container_exec ${CONTAINER_IMAGE} ${COMMAND} ${PARAMS}

######################
#  FUNCTIONAL TESTS  #
#                    #
# Dont include in    #
# runner-template.sh #
######################

function cleanup() {
    rm -f .container_exec.*
}

function validate_output() {
    return 0
}

function run_tests() {
    validate_output
    return 0
}

run_tests && \
    echo "Success" && \
    cleanup
```

The same lines in `runner_template.sh` are included in the top of this file. Also
included are a bash shebang, the variables `output_dir` and `fastq` are set to 
the location of the example data, and template functional tests are at the bottom.
You can test your workflow by executing:
```
% bash tester.sh
```

Make sure the variable definitions and your current working directory resolve
correctly.


---
Return to the [API Documentation Overview](../index.md)
