---
layout: page
title: Create the Client API Keys
tagline:
---

The Agave CLI is used to configure your user environment to interact with the 
SD2E platform. If you do not yet have the CLI installed and in your `$PATH`, first
go [here](install_cli.md).

<br>
#### Initiate with the SD2E Agave tenant:

On the command line, perform: 
```
% tenants-init -t sd2e

You are now configured to interact with the APIs at https://api.sd2e.org/
```

This new configuration is stored in an Agave configuration file located in your
home directory: `~/.agave/current`

<br>
#### Create API keys

API keys are used to authenticate and store your user credentials for a specific 
client applications. To generate API Keys:
```
% clients-create -S -N sd2e_client -D "My client used for interacting with SD2E"
API username:   # enter TACC username
API password:   # enter TACC password

Successfully created client sd2e_client
key: a7vddofNefw4j95Uwcd_fdJHdtsIa
secret: Wsdhy_ckT3aneUWfVyZfdagFagLja
```

Keep your kes secure and do not share with others.

<br>
#### Listing and deleting API keys

To find client keys you have previously generated, you can use the `clients-list`
command:
```
% clients-list
API username:   # enter TACC username
API password:   # enter TACC password

DefaultApplication
sd2e_client
```

In this case, two clients exist for this tenant: the default `DefaultApplication`
and newly generated `sd2e_client`. If needed, you can delete then regenerate 
client API keys with the follwing:
```
% clients-delete [CLIENT_NAME]
```

<br>
#### The ~/.agave/current configuration file

In addition to the tenant ID, your API keys are stored in the agave configuration
file. To view the contents of the file:
```
% cat ~/.agave/current
```

CLI commands can be used to manage different configuration files for swapping
between tenant and user space.

---
Return to the [API Documentation Overview](../index.md)
