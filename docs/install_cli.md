### Install and Configure the CLI

The primary method for interacting with the SD2E platform is the Agave command
line interface (CLI). The CLI is a collection of ~150 shell scripts for managing
authorization, files, systems, jobs, and more. 

There are three methods to install the CLI:
1. Run an installer script to install in ~/sd2e-cloud-cli/bin/
2. Manual install to a location of your choosing
3. Pull a CLI Docker image

#### 1. Run an installer script to install in ~/sd2e-cloud-cli/bin/

Open a terminal window and perform the following:

```
# Run the installer
% curl -L https://raw.githubusercontent.com/sd2e/sd2e-cli/master/install/install.sh | sh

# Source your .bashrc
% source ~/.bashrc

# Verify the CLI is installed
% sd2e info

DARPA SD2E version 1.0.1
TACC Cloud API tenant: sd2e
TACC Cloud API versions:
        Science APIs: 2.2.5
        Reactors API: dev
        TACC Accounting API: v1
```

#### 2. Manual install to a location of your choosing

Open a terminal window and perform the following:

```
# Navigate to a location of your choosing
% mkdir sd2e-project && cd sd2e-project

# Download and unpack the CLI
% curl -L https://raw.githubusercontent.com/sd2e/sd2e-cli/master/sd2e-cloud-cli.tgz -o sd2e-cloud-cli.tgz
% tar -xvzf sd2e-cloud-cli.tgz

# Add the CLI executables to your $PATH
% echo "PATH=\$PATH:$PWD/sd2e-cloud-cli/bin" >> ~/.bashrc
% source ~/.bashrc

# Verify the CLI is installed
% sd2e info

DARPA SD2E version 1.0.1
TACC Cloud API tenant: sd2e
TACC Cloud API versions:
	Science APIs: 2.2.5
	Reactors API: dev
```

#### 3. Pull a CLI Docker image

```
# Pull the latest image
% docker pull sd2e/cloud-cli:latest
% docker run -it -v $HOME/.agave:/root/.agave sd2e/cloud-cli bash
```

#### Influential environment variables

AGAVE_TENANT
AGAVE_USERNAME
AGAVE_KEY
AGAVE_JSON_PARSER = json_mirror | jq | json | python | native
AGAVE_DISABLE_AUTO_REFRESH


