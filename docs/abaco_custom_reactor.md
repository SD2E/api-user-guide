---
layout: page
title: Abaco Command Line Interface
tagline:
---

The Abaco CLI is a command line interface for working with the Abaco (Actor BAsed COntainers) API. Abaco containers can react to events like the successful upload of a file, or the completion of an agave job. Abaco helps with automation by triggering "reactions" do a defined set of events. Abaco reactors are designed to react quickly. If you are deciding between using an agave app or an Abaco reactor; if the computation will take place in under 1 minute, use a Abaco reactor, if the computation will need more than 1 minute to process, you should probably create an agave app.

<br>
#### Creating an Abaco container from Scrath

The Abaco cli comes bundled with the SD2E agave cli. To make sure you have the most current version, run:
```
sd2e info --upgrade
```

To use Abaco, you'll need the following installed on your machine:

1. [Docker CE](https://www.docker.com/community-edition) and you'll need to create a Docker Hub account
2. [jq](https://stedolan.github.io/jq/)
3. getopts (installed by default on macOS High Sierra)


<br>
#### Create skeleton for Abaco Reactor

To create an Abaco reactor, create a directory where you'd like to organize your reactors. Abaco reactors can be created from an existing docker container using:
```
abaco init -n tutorial jturcino/abaco-trial:latest
```
or more generally:
```
abaco init -n Reactor_Name Docker_org/Image_name:version
```

After executing this command you will see a new directory get created with your Reactor name. This directory contains all the required files to deploy an abaco reactor:
```
Dockerfile          
TEMPLATE.md
config.yml          
message.json        
reactor.py          
reactor.rc          
requirements.txt    
secrets.json.sample
```

1. Dockerfile
* All that's mandatory is the `FROM` statement. If you need to manually add
additional code, dependencies, etc. to your Reactor, this is the place for
doing that.

2. Template.md
* This description of the necessary files to create a reactor.

3. The function file (reactor.py)
* The file `reactor.py` is where the code for your function can be found. If
you need to add more files, extend the `Dockerfile`:
```
ADD mycode.py /
```

4. Deployment configuration (reactor.rc)
* The CLI reads from `reactor.rc` to automatically set certain attributes of
the Reactor. Of these, only `REACTOR_NAME` is mandatory.

5. Optional configuration files
* We've tried to automate the repetitive parts of deploying functions to the
by making Reactors runtime and build processes smart (but not too clever for
their own good). The files described below unlock that automation.

6. Function configuration (config.yml)
* If you import the `reactors` module as show in some of the examples, you'll
automatically gain access to an [`AttrDict`][] named `settings` populated by the
contents of `config.yml`.

7. Message template (message.json)
* To activate automatic validation of incoming JSON messages to your Reactor
add a valid [JSON schema][] (draft 4+) by extending the Dockerfile:
```
ADD message.json /
```
At present, the JSON schema *must* be named `AbacoMessage`

8. Python dependencies (requirements.txt)
* This is empty by default. If you have additional Python dependencies beyond
those that ship with the Reactors base image, add them here and they will be
included and built (if possible) at `deploy` time or when you manually run
`docker build`.

9. Ignore files
* We've preconfigured .dockerignore and .gitignore files for you that are
tailored towards preventing you from including sensitive information and/or
build cruft in the Docker images and git repositories used in creating Reactors.

10. Secrets file
* If you create a file called `secrets.json` it will never be committed to a Git
repository or included in a Docker image build, but `abaco deploy` use it to
populate the default environment variables for the Reactor. vValues placed in this
file are *NOT THAT SECRET* since they can be discovered by other users if you
choose to share your Reactor.

#### Building and Deploying your Reactor

Once you've added the necessary information to the files listed above, you can deploy your reactor by executing this command from your reactor directory:
```
abaco deploy
```
Or if you'd like to just build (but not deploy) the reactor, in order to test functions locally, you can use:
```
abaco depoly -R
```
Once deployed, abaco will return an 13-digit alpahnumerical hex which will serve as your Reactor ID.

---
Return to the [API Documentation Overview](../index.md)
