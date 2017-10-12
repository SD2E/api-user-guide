---
layout: page
title: Create Custom Applications
tagline: Containerize your executable
---

If an image of your executable already exists, and was created by a trusted source,
consider using that rather than building your own. You may find existing images
on hubs such as
[Docker Hub](https://hub.docker.com/) or
[BioContainers](https://biocontainers.pro/registry/).

This tutorial is a quick and dirty summary of how to build your own Docker image
as if there is not one available for your executable. This is not meant to replace
the full [Docker documentation](https://docs.docker.com/develop/). Please note
that [Singularity images](http://singularity.lbl.gov/docs-hpc) are also amenable
to this infrastructure.

We will continue with the example of FastQC from the
[previous page](create_app_01.md).

<br>
#### Choose a source image

*Prerequisite: You should have a [Docker ID](https://hub.docker.com) and 
`docker` should be installed on your local machine*

The only dependency for [FastQC](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/)
is a reasonably recent Java Runtime Environment.
Thus, most modern Linux OS-es should suffice. The SD2E Docker hub provides base
images for this. To begin, pull the `ubuntu16` base image from the SD2E Docker hub:
```
% docker pull sd2e/base:ubuntu16
```

Also, now is a good time to prepare the `src/` directory in the applicaiton
bundle for installation. Navigate to that directory and remove the template
script, which we do not need:
```
% cd ~/fastqc/src/
% rm hello.sh
```

<br>
#### Install and test interactively


The installation process for fastqc is extremely simple. It is good practice to
test the installation interactively, and record the steps for your Dockerfile:
```
# Start an interactive docker session
% docker run --rm -it sd2e/base:ubuntu16

# Update and install necessary packages
[docker] % apt-get update
[docker] % apt-get upgrade -y
[docker] % apt-get install wget -y
[docker] % apt-get install zip -y
[docker] % apt-get install default-jre -y

# Install FastQC
[docker] % wget https://www.bioinformatics.babraham.ac.uk/projects/fastqc/fastqc_v0.11.5.zip
[docker] % unzip fastqc_v0.11.5.zip
[docker] % rm fastqc_v0.11.5.zip
[docker] % chmod +x /FastQC/fastqc
```

After a bit of trial and error, the commands above are
the shortest path to install the `fastqc` executable. You can test it from within
the docker image to make sure it is working by, for example:
```
[docker] % /FastQC/fastqc -h

            FastQC - A high throughput sequence QC analysis tool

SYNOPSIS

        fastqc seqfile1 seqfile2 .. seqfileN

    fastqc [-o output dir] [--(no)extract] [-f fastq|bam|sam]
           [-c contaminant file] seqfile1 .. seqfileN
...etc
```

<br>
#### Note on source code and mounting directories

In this instance, we could have downloaded the source zip file for FastQC directly
to our `src/` directory, then mounted that directory within the image, e.g.:
```
% pwd
~/fastqc/src/
% wget https://www.bioinformatics.babraham.ac.uk/projects/fastqc/fastqc_v0.11.5.zip
% docker run --rm -it -v $PWD:/src sd2e/base:ubuntu16
...etc
```
That route is perfectly reasonable and can be followed here. However, some packages 
have very large zip or tar.gz files (100s of MB), and would be cumbersome to keep 
in this `fastqc` app bundle folder. It is up to the app developer to find the balance
between completeness of source files and responsible disk usage.

Here, we decide to not download the source permanently. Instead, we make a
record of where the source came from. For example:
```
% pwd
~/fastqc/src/
% echo "Source: https://www.bioinformatics.babraham.ac.uk/projects/fastqc/fastqc_v0.11.5.zip" >> README.md
```

<br>
#### Write the Dockerfile

Next, translate the steps required to install your software package into a
resonable `Dockerfile`. The `Dockerfile` should be located at the root directory,
`~/fastqc/Dockerfile`:
```
FROM sd2e/base:ubuntu16

RUN apt-get update 
RUN apt-get upgrade -y 
RUN apt-get install wget -y
RUN apt-get install zip -y 
RUN apt-get install default-jre -y 

RUN wget https://www.bioinformatics.babraham.ac.uk/projects/fastqc/fastqc_v0.11.5.zip
RUN unzip fastqc_v0.11.5.zip 
RUN rm fastqc_v0.11.5.zip
RUN chmod +x FastQC/fastqc 

ENV PATH "/FastQC/:$PATH"
```

The final line of this file adds the location of the executable to the `PATH` so
it is easier to call from the command line.

<br>
#### Build and test the image

Given that the `Dockerfile` is in your current directory, the command to build
a new Docker image is:
```
% docker build -f Dockerfile --force-rm -t fastqc:0.11.5 ./
```

Once built, test the new image with an example command:
```
% docker run fastqc:0.11.5 fastqc -h
- or -
% docker run fastqc:0.11.5 perl /Fastqc/fastqc -h
```

*Note: Calling the complete path to executables is sometimes safer than relying
on PATH environment variables, especially when translating between Docker and
Singularity*

If you see the FastQC help text, the installation likely was successful.
At this time, it might be prudent to set up a test with real data as well. The
template app does not have this directory, but we can add an `example` directory
to our root to hold example data:
```
% cd ~/fastqc/
% mkdir example/
% cd example/

# Download random sample data or provide your own
% wget https://molb7621.github.io/workshop/_downloads/SP1.fq
```

Next, run the FastQC pipeline on the example data:
```
% docker run -v $PWD:/data fastqc:0.11.5 perl /FastQC/fastqc /data/SP1.fq
```

If successful, you should find the output files `SP1_fastqc.html` and `SP1_fastqc.zip`
in the `~/fastqc/example/` directory.


<br> 
#### Push your image to the cloud

Finally, push your Docker image to a publicly available repository. It can be
your own personal repository as long as it is set to public, and not private.
To push to your own repository, first tag it with your Docker ID:
```
% docker tag fastqc:0.11.5 USERNAME/fastqc:0.11.5
```

In the above, replace `USERNAME` with your Docker ID. Next, edit the
`~/fastqc/build.sh` script as follows:
```
#!/usr/bin/env bash

CONTAINER_TAG="USERNAME/fastqc:0.11.5"

docker build -t ${CONTAINER_TAG} . && docker push ${CONTAINER_TAG}
```

And execute the script with `bash` to push your image to the cloud:
```
% bash build.sh
```

Next, we will continue to edit files in the app bundle to call this new image.

---
Proceed to [Build wrapper and test scripts](create_app_03.md)

Go back to [Create Custom Applications](create_app.md)

Return to the [API Documentation Overview](../index.md)
