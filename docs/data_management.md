---
layout: page
title: Data Management in SD2E
tagline:
---

Data is managed using the `files` service of the Agave CLI. `Files` commands can
be used to upload and download data, remotely manage and organize data, importing
external data.

`Files` commands act on Agave `systems`. See [generating systems](generate_systems.md)
for more details.

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

Here is a quick summary of the storage systems:

* `data-tacc-work-username`: Your personal directory on the TACC `$WORK` filesystem. You have +rw and sharing access. This will show up in the portal as **My Data**.
* `data-sd2e-community`: Project-wide shared data. Unless you are specifically authorized, it is read-only. This will show up in the portal as **Community Data**.
* `data-sd2e-projects-*`: These are subproject storage spaces. Your access rights will be variable. These will show up in the portal under **My Projects**.
* `data-sd2e-app-assets`: This system is provate to SD2E staff and read-only. It will not show up in the portal.

Next, we will practice file operations on your personal storage system,
`data-tacc-work-username`. (Replace `username` with your TACC username).

<br>
#### Listing files and navigating

To list the files available to you on the storage system, use:
```
% files-list -S data-tacc-work-username -L /
drwx------  username  4096  13:07  .
```

The `-L` flag provides a long listing (similar to `ls -l` in Linux), and the
forward slash at the end of the line indicates the root path - your `$WORK`
directory.

To make a new folder, then list the contents of that folder::
```
% files-mkdir -S data-tacc-work-username -N sd2e-data /
Successfully created folder sd2e-data

% files-list -S data-tacc-work-username -L /
drwx------  username  4096  15:49  .
drwx------  username  4096  15:49  sd2e-data

% files-list -S data-tacc-work-username -L /sd2e-data/
drwx------  username  4096  15:49  .
```

<br>
#### Uploading and downloading via the CLI

Upload a file from your local system to the storage system with:
```
% touch my_local_file.txt
% files-upload -S data-tacc-work-username -F my_local_file.txt /sd2e-data/
Uploading my_local_file.txt...
######################################################################## 100.0%

% files-list -S data-tacc-work-username -L /sd2e-data/
drwx------  username  4096  15:53  .
-rw-------  username  0     15:53  my_local_file.txt
```

Copy the file to something with a new name, and download the result:
```
% files-copy -S data-tacc-work-username -D /sd2e-data/my_local_file.txt /sd2e-data/copy.txt
Successfully copied /sd2e-data/my_local_file.txt to /sd2e-data/copy.txt

% files-list -S data-tacc-work-username -L /sd2e-data/
drwx------  username  4096  15:57  .
-rw-------  username  0     15:57  copy.txt
-rw-------  username  0     15:53  my_local_file.txt

% files-get -S data-tacc-work-username /sd2e-data/copy.txt
######################################################################## 100.0%

% ls
copy.txt  my_local_file.txt
```

<br>
#### Other file operations

Files can also be moved, deleted, and renamed using the `files-move` and `files-delete`
commands, with similar syntax to the `files-copy` command.
```
% files-move

% files-delete
```

Be cautious with `files-move` and `files-delete` commands. Just like a Linux
filesystem, files inadvertently deleted or written over are probably unrecoverable.

<br>
#### Set your default system

You may find that most of the file operations you perform are on your private 
TACC storage system - data-tacc-work-username. To avoid typing the full name of
the system on every command line, set it as default with:
```
% systems-setdefault data-tacc-work-username
Successfully set data-tacc-work-username as the default STORAGE system for username
```

<br>
#### Coming soon

Files import from unauthenticated HTTP

Sharing with another username

The agave:// URL convention


<br>
#### Get help

Reminder: at any time, you can issue an Agave command with the `-h` flag to
find more information on the function and usage of the command.


---
Return to the [API Documentation Overview](../index.md)
