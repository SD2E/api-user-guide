---
layout: page
title: Create Custom Applications
tagline: Containerize your app
---

If an image of your app already exists, and was created by a trusted source,
consider using that rather than building your own. You may find existing images
on hubs such as
[Docker Hub](https://hub.docker.com/) or
[BioContainers](https://biocontainers.pro/registry/).

This tutorial is a quick and dirty summary of how to build your own Docker image
as if there is none available for your application. This is not meant to replace
the full [Docker documentation](https://docs.docker.com/develop/). Please note
that [Singularity images](http://singularity.lbl.gov/docs-hpc) are also amenable
to this infrastructure.

<br>
#### Choose a source image

*Prerequisite: `docker` should be installed*

The only dependency for [FastQC](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/)
is a reasonably recent Java Runtime Environment.
Thus, most modern Linux OSes should suffice. The SD2E Docker hub provides base
images for this. To begin, pull the ubuntu16 base image from the SD2E Docker hub:
```
% docker pull sd2e/base:ubuntu16
```

Also, now is a good time to prepare the `src/` directory in the applicaiton
bundle for installation. Navigate to that directory and remove the template
script:
```
% cd fastqc/src/
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
[docker] % chmod +x FastQC/fastqc
```

After a bit of trial and error, the commands above are the steps I found were
required to install the `fastqc` executable. You can test to make sure it is
working by, for example:
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
to our `src/` directory, then mounted that directory within the image. That path
is perfectly reasonable and can be followed here. However, some packages have very
large zip or tar.gz files (100s of MB), and would be cumbersome to keep in our
`fastqc` app folder. It is up to the app developer to find the balance between
completeness of data and disk usage.

Here, we decide to not download the source permanently, but it is good practice
to make a record of where the source came from. For example:
```
% pwd
~/fastqc/src/
% echo "Download the source from https://www.bioinformatics.babraham.ac.uk/projects/fastqc/fastqc_v0.11.5.zip" >> README.md
```

<br>
#### Write the Dockerfile

Take the steps required to install your package and translate them into a
resonable Dockerfile. The Dockerfile should be located at the root directory:
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

<br>
#### Try building the image and make sure it works

The command to build your new image based on your docker container looks like:
```
% docker build -f Dockerfile --force-rm -t fastqc:0.11.5 ./
```

Test your image with an example command:
```
% docker run fastqc:0.11.5 fastqc -h
```

If you see the help text, odds are the install went correctly. To do a more
advanced test on real data, try something like:
```
% ls
my_example_data.fq
$ docker run -v $PWD:/data fastqc:0.11.5 fastqc -o /data/ /data/my_example_data.fq
```


<br> 
#### Push your image to the cloud

Finally, push your Docker image to a publically available repository. It can be
your own personal repository as long as it is set to public, and not to private:
```
% docker push fastqc:0.11.5
```

---
Return to the [API Documentation Overview](../index.md)
