---
layout: page
title: Create Private Systems
tagline:
---

Most standard SD2E API users will not need to create custom execution or storage
systems. By default, you have access to some private and public execution and
storage systems that should connect you to [most of TACC resources](systems_basics.md).

Even so, there are circumstances where it is advantageous to create your own systems.
For example, they can help you connect to private lab servers, license servers,
cloud instances, and external clusters. For the purposes of this example, we will
register a [Jetstream instance](https://jetstream-cloud.org/) as both a storage
and execution system.

<br>
#### Gather information

For any system you register with Agave, you need the hostname and login credentials.
The preferred login credentials are SSH key pairs. For instructions on creating
appropriate public / private keys, see [here](ssh_keys.md).

You also need some basic information on the compute resources which will be
registered as systems. For storage systems, you need to know the desired path
for storing files. For execution systems, you need to first decide if it will be
an HPC or CLI execution system. If an HPC execution system, you need some 
information about the queueing system (queue names, limits, etc.). For both types
of execution system you need a path for running jobs.

The Jetstream instance we will register has hostname `129.114.104.39`. We will 
assume the user '`username`' has already created [SSH keys](ssh_keys.md) and added
the public key to `~/.ssh/known_hosts/`. The path for storing data will be the 
home directory, `/home/username/`. Since this instance does not have a queuing 
system, we will register it as a CLI execution system and run jobs from 
the home directory, `/home/username/`.

<br>
#### Register a storage system

Systems are stored in Agave as `json` objects. For a complete list of all valid
fields in system descriptions, see the [Agave documentation](http://developer.agaveapi.co/#systems).
Instead of creating a new `json` system description by hand, start with the following
storage system template:
```
{
  "id": "jetstream-storage-username",
  "name": "My Jetstream Storage System",
  "description": "My system for storing data on jetstream instance 129.114.104.39",
  "status": "UP",
  "type": "STORAGE",
  "storage": {
    "host": "129.114.104.39",
    "port": 22,
    "protocol": "SFTP",
    "rootDir": "/home/username",
    "homeDir": "/",
    "auth": {
      "publicKey": " <enter public key here> ",
      "privateKey": " <enter private key here> ",
      "username": "username",
      "type": "SSHKEYS"
    }
  }
}
```
Most are self explanatory, but here is a brief breakdown of the important fields:
* `id`: this is how the system name appears for `systems-list` commands, must be unique
* `name`: common display name for the system
* `status`: functional status, must be `UP` to be used
* `type`: can be `STORAGE` or `EXECUTION`
* `host`: IP address or hostname of system
* `rootDir`: path to home directory (or target storage directory)
* `homeDir`: path relative to `rootDir` for `files-*` operations
* `publicKey`: cut and paste your public key here
* `privateKey`: cut and paste your private key here
* `username`: this is your username for the target system

After inserting them into the template, The public and private key should be pasted
on one line each (wrapping around) similar to the following:
```
    "auth":{
      "publicKey": "ssh-rsa AAAAB3NzaC1yc2EBBAADAQABMQPRgQChJ6bzejqSuJdTi+VwMif8qotuSSlYwrVt0EWVduKZHpzOnS1zlknAyYXmQQFcaJ+vNAQayVMTqv+A+1lzxppTdgZ0Dn42EOYWRa6B/IEMPzDuKb7F0qNFiH9m+OZJDYdIWS1rlN1oK32jHUi0xV8kM3KOLf2TIjDBUyZRpMGyQ= user@email.com",
      "privateKey": "-----BEGIN RSA PRIVATE KEY-----nMIVCXAIBAAKBgQRhJ6bzejqSuJdTi+VwMif8qoyuSSlYwrVt0EWVdkFvA+wmxlOcnLMJOYotSyu0JqY/TeW6reNBMkTkVU8FgXJ2k+4agNrphxKCWmQbC4Xm+CW5N6HiIBZo/TxzDaAmsNGklmVfZGO+8cCDqdKIlF0hqxytI8GgtiHImg2j+nwcIQT3ojER45I+6hYLj95HnSyyC7rEtjIBCvW8FVmT7JCDnS0BwAkmnRt0NPzrliEk1k+swkCTp3SOHSk4SsJPuLcC7OW6pkjD6AyHV4ZrYy0US/Z+Zmn01Lhgw0sNjQL8PyJuVeFysp9Sr40c77OYbVGbOAJGKGtYsD6x3/0Cvz+vqQ0VpQPCOiMf2tytglUNBkiEVThkm+Nl36yxRmpcGCLEh9EGTWNuD++ZT+nHka6MvIN2NSsXJD32sw15g4A0bmzSXnbfFg8TBAjGTDW7l0P8prFrtQ8Wml14390b29l1ptAyE=n-----END RSA PRIVATE KEY-----",
      "username": "username",
      "type": "SSHKEYS"
    }
```

Save the template in a file called `jetstream-storage-username.json`. Never share
this file with anyone else because it contains a plain text copy of your private key.
To register this system with Agave, use the following command:
```
% systems-addupdate -F jetstream-storage-username.json
```

<br>
#### Register an execution system

An execution system contains many of the same fields as a storage system. Use 
the following template to begin:
```
{
  "id": "jetstream-execution-username",
  "name": "My Jetstream Execution System",
  "description": "My system for executing CLI jobs on jetstream instance 129.114.104.39",
  "status": "UP",
  "type": "EXECUTION",
  "executionType": "CLI",
  "scheduler": "FORK",
  "maxSystemJobs": 50,
  "maxSystemJobsPerUser": 50,
  "scratchDir": "/scratch",
  "login": {
    "host": "129.114.104.39",
    "port": 22,
    "protocol": "SSH",
    "proxy": null,
    "auth": {
      "publicKey": " <enter public key here> ",
      "privateKey": " <enter private key here> ",
      "username": "username",
      "type": "SSHKEYS"
    }
  },
  "storage": {
    "host": "129.114.104.39",
    "port": 22,
    "protocol": "SFTP",
    "rootDir": "/home/username",
    "homeDir": "/",
    "auth": {
      "publicKey": " <enter public key here> ",
      "privateKey": " <enter private key here> ",
      "username": "username",
      "type": "SSHKEYS"
    }
  }
}
```
Some of the new fields in this execution system include:
* `type`: execution system rather than storage system
* `executionType`: `CLI` instead of `HPC`
* `scheduler`: no scheduler for `CLI` system, so use `FORK`
* `maxSystemJobs`: maximum concurrent jobs on the system
* `scratchDir`: path for job working directory relative to `rootDir`
* `login`: similar to `storage`, host and credential information

For this execution system, there are two locations to cut and paste your SSH keys.
Again, because keys will be stored in plain text in this file, do not share this
file with anyone and keep it secure .More fields, including information about how to submit jobs to an `HPC` system
using a queue can be found in the [Agave documentation](http://developer.agaveapi.co/#execution-systems).

Save the template in a file called `jetstream-execution-username.json`. 
To register this system with Agave, use the following command:
```
% systems-addupdate -F jetstream-execution-username.json
```


<br>
#### Other helpful tools

To dynamically generate an execution system json file, try the website below:

<http://cyverse.github.io/cyverse-sdk/docs/register-your-cluster.html>


---
Return to the [API Documentation Overview](../index.md)
