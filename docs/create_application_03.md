---
layout: page
title: Create Custom Applications
tagline: Build wrapper and test scripts
---

If you have been following along, your fastqc app directory should contain:
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
#### Write the test script

The test script allows you to test your app with real data as it will be run in
production. We use shell variables liberally in the script to facilitate
integration with Agave later. Edit the test script located at
`~/fastqc/fastqc-0.11.5/tester.sh` as follows:
```
#!/usr/bin/env bash

fastq="/example/SP1.fq"
example_mount="-v ${PWD}/../example:/example:rw"

CONTAINER_IMAGE="index.docker.io/USERNAME/fastqc:0.11.5"

. _util/container_exec.sh

COMMAND="fastqc"
PARAMS="${fastq}"

DEBUG=1 container_exec ${example_mount} ${CONTAINER_IMAGE} ${COMMAND} ${PARAMS}

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

The `fastq` variables is the only runtime variable used in this
app (provided by the user through Agave). It is explicitly defined here for testing
purposes, but will be removed for the purposes of the wrapper script. Similarly,
the `example_mount` directory is also mounted to the container only for purposes
of testing, but will not be mounted in production.

For the `CONTAINER_IMAGE` variable,
replace `USERNAME` with your Docker ID (or project space where the image is
located). Also in the above file, the `container_exec.sh` script is sourced, `fastqc`
is the command to execute, and the parameter passed to the command is
the input fastq file (`${fastq}`).
The final command in the script (begging with `DEBUG=1`) is the main container
run command. It is written in this way to be flexible and compatible to both
Docker and Singularity.

At the bottom of the script are a few template functions that you can develop
to validate that the test ran correctly. Here,
they are blank, and it is up to the user to write appropriate tests if desired.
Test your workflow by executing:
```
% bash tester.sh
+ docker run --rm --user=0:20 -v /Users/username/fastqc/fastqc-0.11.5:/ home:rw -w /home -v /Users/username/fastqc/fastqc-0.11.5/../example:/ex ample:rw index.docker.io/USERNAME/fastqc:0.11.5 fastqc /example/SP1.fq
Started analysis of SP1.fq
Analysis complete for SP1.fq
+ '[' '!' -z 1 ']'
+ set +x
Success
```

A successful test will result in `SP1_fastqc.html` and `SP1_fastqc.zip` files
generated in the `~/fastqc/example/` folder. (Make sure not to confuse old output
files with new output files - check the time stamps or remove the output and run
again).

<br> 
#### Write the wrapper script

The file loacted at `~/fastqc/fastqc-0.11.6/runner-template.sh` contains runtime
instructions for your app in Agave. Essentially, it is a subset of your test 
script. Copy the test script to `runner_template.sh` and retain only the following
lines:
```
CONTAINER_IMAGE="index.docker.io/USERNAME/fastqc:0.11.5"

. _util/container_exec.sh

COMMAND="fastqc"
PARAMS="${fastq}"

container_exec ${CONTAINER_IMAGE} ${COMMAND} ${PARAMS}
```

(Replace `USERNAME` with your Docker ID).
Notice now we do not define `fastq`, as it will be defined
by Agave. We also do not mount our example data into the Docker container,
as that will be taken care of in production.

---
Proceed to [Add your app to the SD2E tenant](create_application_04.md)

Go back to [Create Custom Applications](create_application.md)

Return to the [API Documentation Overview](../index.md)
