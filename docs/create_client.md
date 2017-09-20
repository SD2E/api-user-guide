### Create the API Client Key

The Agave CLI is used to configure the user environment to interact with the 
SD2E platform. If you do not yet have the CLI installed and in your $PATH, first
go [here](install_cli.md).

Your user environment can be set up in one of two ways:
1. Automatically using the set up script
2. Manually using the CLI


#### 1. Automatically using the set up script

(forthcoming)

#### 2. Manually using the CLI

On the command line, perform the following:

```
# Initiate with the SD2E tenant
%tenants-init -t sd2e

# Create a client (enter TACC username and password)
%clients-create -S -N my_client -D "My client used for interacting with SD2E"

# Create an auth token (enter TACC password)
%auth-tokens-create -S

# Check the status of the token
% auth-check

# Refresh an expired token:
% auth-tokens-refresh
```
