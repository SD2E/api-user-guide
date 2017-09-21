---
layout: page
title: Create the API Client Key
tagline:
---

The Agave CLI is used to configure the user environment to interact with the 
SD2E platform. If you do not yet have the CLI installed and in your `$PATH`, first
go [here](install_cli.md).

Your user environment can be set up in one of two ways:
1. Automatically using the set up script
2. Manually using the CLI

<br>
#### Automatically using the set up script

(coming soon)

<br>
#### Manually using the CLI

On the command line, first initiate with the SD2E tenant:
```
%tenants-init -t sd2e

```

Then, create a client key for interacting with that tenant using your TACC
credentials:
```
%clients-create -S -N my_client -D "My client used for interacting with SD2E"
Username:   # enter TACC username
Password:   # enter TACC password
```


#### The ~/.agave/current configuration file

All the information generated above is stored in a configuration file in your 
home directory. CLI commands can be used to manage different configuration files
for swapping between tenant and user space.

---
Return to the [API Documentation Overview](../index.md)
