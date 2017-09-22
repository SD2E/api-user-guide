---
layout: page
title: HPC and STORAGE Systems Basics
tagline:
---

An Agave *system* is a server or collection of servers associated with a single
hostname. They may be public or private, and they may either be STORAGE systems
(used for storing files) or HPC systems (used for running jobs).

In SD2E, we provide all users with private HPC and STORAGE systems attached to
TACC resources. If you would like to create your own systems attached to different
resources, skip ahead to [Creating Systems](create_systems.md).


<br>
#### Find your systems

Systems where you are able to store and access data (called STORAGE systems) can
be found using the `systems-list` command:
```
% systems-list -S
data-sd2e-projects-q0-ingest
data-sd2e-projects-q0-examples
data-tacc-work-username
data-sd2e-community
data-sd2e-app-assets
```
Here is a quick summary of the default STORAGE systems:

* `data-tacc-work-username`: Your personal directory on the TACC `$WORK` filesystem. You have +rw and sharing access. This will show up in the portal as **My Data**.

* `data-sd2e-community`: Project-wide shared data. Unless you are specifically authorized, it is read-only. This will show up in the portal as **Community Data**.

* `data-sd2e-projects-*`: These are subproject storage spaces. Your access rights will be variable. These will show up in the portal under **My Projects**.

* `data-sd2e-app-assets`: This system is private to SD2E staff and read-only. It will not show up in the portal.

<br>
Systems where you are able to execute commands and run jobs (called HPC systems)
can be found with the following command: 
```
% systems-list -E
hpc-tacc-maverick
cli-tacc-maverick
```

(*Tip: use "`systems-list -h`" to see more usage options*)

<br>
#### Verbose system information

To see additional system information, including hostname, root folder locations,
and availability, use the verbose flag with a system ID:
```
% systems-list -v data-tacc-work-username
{
  "owner": "vaughn",
  "_links": {
    "owner": {
      "href": "https://api.sd2e.org/profiles/v2/vaughn"
    },
    "metadata": {
      "href": "https://api.sd2e.org/meta/v2/data/?q=%7B%22associationIds%22%3A%223474211147285074407-242ac11a-0001-006%22%7D"
    },
    "credentials": {
      "href": "https://api.sd2e.org/systems/v2/data-tacc-work-username/credentials"
    },
    "roles": {
      "href": "https://api.sd2e.org/systems/v2/data-tacc-work-username/roles"
    },
    "self": {
      "href": "https://api.sd2e.org/systems/v2/data-tacc-work-username"
    }
  },
  "available": true,
  "description": "",
  "storage": {
    "proxy": null,
    "protocol": "SFTP",
    "mirror": false,
    "port": 22,
    "auth": {
      "type": "SSHKEYS"
    },
    "publicAppsDir": null,
    "host": "data.tacc.utexas.edu",
    "rootDir": "/work/projects/SD2E-Community/prod/home/username",
    "homeDir": "/"
  },
  "type": "STORAGE",
  "uuid": "3474211147285074407-242ac11a-0001-006",
  "revision": 2,
  "site": "tacc.utexas.edu",
  "default": true,
  "public": false,
  "globalDefault": false,
  "name": "Your TACC Work Directory",
  "id": "data-tacc-work-username",
  "lastModified": "2017-09-20T14:15:40.000-05:00",
  "status": "UP"
}
```
*---Update to WORK filesystem when available---*

As described above, this STORAGE system is a gateway to your private storage
space on the TACC `$WORK` filesystem. The `rootDir` is the virtual root path
for operations performed on this system.

<br>
#### Set a default system

You may find that most of the [file operations](data_management.md) you perform
are on your private TACC STORAGE system - `data-tacc-work-username`. To avoid
typing the full name of the system with every file command, you may set it as the
default with:
```
% systems-setdefault data-tacc-work-username
Successfully set data-tacc-work-username as the default STORAGE system for username
```


---
Return to the [API Documentation Overview](../index.md)
