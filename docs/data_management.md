---
layout: page
title: Data Management
tagline: File Operations
---

Data is managed using the `files` service of the Agave CLI. With many parallels in
Linux file commands (`ls, mv, cp, rm,` etc.), Agave `files-*` commands can be used to 
list files, upload and download data, remotely manage and organize data, and import
external data from the web.

Agave `files-*` commands act on Agave `systems`. See the [systems basics](systems_basics.md)
documentation for more details.

This page is broken down into the following sections:

1. [Listing files and navigating](#listing-files-and-navigating)
2. [Uploading and downloading via the CLI](#uploading-and-downloading-via-the-cli)
3. [Other file operations](#other-file-operations)

<br>
#### Listing files and navigating

To list the files available to you on the STORAGE system, use:
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

% files-list -S data-tacc-work-username -L sd2e-data/
drwx------  username  4096  15:49  .
```

*Tip: if you set `data-tacc-work-username` as your default system, then you 
do not need to provide it on the command line. More info [here](systems_basics.md).*

<br>
#### Uploading and downloading via the CLI

Upload a file from your local system to the STORAGE system with:
```
% touch my_file.txt
% files-upload -S data-tacc-work-username -F my_file.txt sd2e-data/
Uploading my_file.txt...
######################################################################## 100.0%

% files-list -S data-tacc-work-username -L sd2e-data/
drwx------  username  4096  15:53  .
-rw-------  username  0     15:53  my_file.txt
```

Copy the file to something with a new name using the `files-copy` command:
```
% files-copy -S data-tacc-work-username -D sd2e-data/my_file.txt sd2e-data/copy.txt
Successfully copied sd2e-data/my_file.txt to sd2e-data/copy.txt

% files-list -S data-tacc-work-username -L sd2e-data/
drwx------  username  4096  15:57  .
-rw-------  username  0     15:57  copy.txt
-rw-------  username  0     15:53  my_file.txt
```

Then download the result:
```
% files-get -S data-tacc-work-username sd2e-data/copy.txt
######################################################################## 100.0%

% ls
copy.txt  my_file.txt
```

<br>
#### Other file operations

Files can also be moved, deleted, and renamed using the `files-move` and `files-delete`
commands, with similar syntax to the `files-copy` command. For example:
```
% files-move -S data-tacc-work-username -D sd2e-data/file2.txt /sd2e/my_file.txt
% files-mkdir -S data-tacc-work-username -N subfolder/ sd2e-data/
% files-move -S data-tacc-work-username -D sd2e-data/subfolder/file2.txt sd2e-data/file2.txt
% files-delete -S data-tacc-work-username sd2e-data/subfolder/file2.txtt
```

Be cautious with `files-move` and `files-delete` commands. Just like a Linux
filesystem, files inadvertently deleted or written over are probably unrecoverable.


<br>
#### Get help

Reminder: at any time, you can issue an Agave command with the `-h` flag to
find more information on the function and usage of the command. Extensive Agave
documentation can be found [here](http://developer.agaveapi.co/).


---
Return to the [API Documentation Overview](../index.md)
