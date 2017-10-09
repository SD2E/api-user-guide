---
layout: page
title: Create Custom Applications
tagline: Clone an example app
---

Here, we will take the first steps in developing our own app. It is assumed that
you already have an app in mind (i.e. a program that you would like to run), and 
you have an idea of how you would like to run it (i.e. what are the inputs and 
outputs, what flags might be used, etc.).

<br>
#### Develop an app by example

On the [SD2E github pages](https://github.com/SD2E/reactors-etl),
you will find examples of several apps that have already
been built. Clone the entire repo using git and navigate into the `reactors` folder:
```
% git clone https://github.com/SD2E/reactors-etl
Cloning into 'reactors-etl'...
remote: Counting objects: 602, done.
remote: Compressing objects: 100% (46/46), done.
remote: Total 602 (delta 18), reused 41 (delta 12), pack-reused 541
Receiving objects: 100% (602/602), 168.23 MiB | 853.00 KiB/s, done.
Resolving deltas: 100% (253/253), done.
Checking out files: 100% (159/159), done.

% cd reactors-etl/
% cd reactors/
% ls
fcs-tasbe/              hello-container/        lcms/                   sailfish/
fcs-tasbe-tacc/         kallisto/               msf/
```

As of this writing, there are seven example apps including `hello-container`,
which is both a simple example app and a template for creating new apps. Spend 
some time navigating through the folders to get an idea of what components are
part of an app bundle.

<br> 
#### Structure of an app

The basic structure of an app looks as follows:
```
hello-container/
├── Dockerfile
├── build.sh
├── deploy.sh
├── hello-container-0.1.0
│   ├── _util
│   │   └── container_exec.sh
│   ├── app.json
│   ├── app.yml
│   ├── job.json
│   ├── runner-template.sh
│   └── tester.sh
└── src
    └── hello.sh
```

At the top level, you will find a `Dockerfile` and `build.sh` script. These are
necessary for building the container reproducibly, and having them will facilitate 
future app updates. The `deploy.sh` script is the final script of the app building
process which helps the user to add their app to the Agave SD2E tenant.

One of the subdirectories is named `hello-container-0.1.0/`. This directory contains
both the `runner-template.sh`, which is a script directing how the app should be
run upon invocation, and `tester.sh`, which is a clone of `runner-template.sh`
for testing locally. Also included are template files `app.json` and `job.json`
which contain Agave-based descriptions of the app itself, and how the app should
be run. The version number in the path name (`-0.1.0`) should be the version of
software that you are installing. If you make updates to this app in the future,
Agave will automatically increment an internal counter, e.g. `-0.1.0u1`, `-0.1.0u2`,
etc. 

Finally, the other subdirectory is a `src/` directory. It contains the source
code and runtime assets (reference files, libraries, etc.) required for your app.
In this example app, the only source is the simple `hello.sh` shell script.


<br>
#### Set up to create your own app

Here, we will create an app for fastq quality control using the publicly available
[FastQC tool](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/).
Copy the `hello-container` template app and change the name to the app you would
like to develop:
```
% cp -r hello-container/ fastqc/
% cd fastqc/
% mv hello-container-0.1.0 fastqc-0.11.5/
```

The contents of the `fastqc/` app bundle should now appear as:
```
fastqc/
├── Dockerfile
├── build.sh
├── deploy.sh
├── fastqc-0.11.5
│   ├── _util
│   │   └── container_exec.sh
│   ├── app.json
│   ├── app.yml
│   ├── job.json
│   ├── runner-template.sh
│   └── tester.sh
└── src
    └── hello.sh
```

Next, we will edit each of the files in turn.

Proceed to [Containerize your app](create_application_02.md)

Go back to [Create Custom Applications](create_application.md)



---
Return to the [API Documentation Overview](../index.md)
