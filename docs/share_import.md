---
layout: page
title: Data Management
tagline: Share and Import
---

This page is broken down into the following sections:

1. [Share files with another user](#share-files-with-another-user)
2. [Share files using postits](#share-files-using-postits)
3. [Import files from other systems](#import-files-from-other-systems)
4. [Import files from the web](#import-files-from-the-web)

<br>
#### Share files with another user

File permissions are managed similar to Linux file permissions. To list the
permissions on an existing file on the STORAGE system, issue:
```
% files-pems-list -S data-tacc-work-username sd2e-data/my_file.txt
```

The output should look similar to:
```
sd2e READ WRITE EXECUTE
vaughn READ WRITE EXECUTE
username READ WRITE EXECUTE
```

To add permissions for another user to view the file, use the `files-pems-update`
command:
```
% files-pems-update -U my_collaborator -P ALL sd2e-data/my_file.txt
% files-pems-list -S data-tacc-work-username sd2e-data/my_file.txt

my_collaborator READ WRITE EXECUTE
sd2e READ WRITE EXECUTE
vaughn READ WRITE EXECUTE
username READ WRITE EXECUTE
```

Now, a user with username `my_collaborator` has permissions to access the file.
Valid values for setting permission with the `-P` flag are READ, WRITE, EXECUTE,
READ_WRITE, READ_EXECUTE, WRITE_EXECUTE, ALL, and NONE. This same action can be
performed recursively on directories using the `-R` flag.

<br>
#### Share files using postits

Another convenient way to share data is the Agave postits service. Postits
generate a short URL with a user-specified lifetime and limited number of uses.
Anyone with the URL can paste it into a web browser, or curl against it on the
command line. To create a postit: 
```
% postits-create -m 5 -l 3600 https://api.sd2e.org/files/v2/media/system/data-tacc-work-username/sd2e-data/my_file.txt
```

The json response from this command is the URL, e.g.:

``` 
https://api.sd2e.org/postits/v2/866d55b36a459e8098173655e916fa15
```

This postit is only good for 5 downloads (`-m 5`) and only available for one hour (3600 seconds, `-l 3600`). The creator of the postit can list and delete their postits with the following commands:

```
% postits-list -V
% postits-delete 866d55b36a459e8098173655e916fa15
```

The long alphanumeric string is the postit UUID displayed by the verbose postits-list command.

<br>
#### Import files from other systems

It may be useful to import data from other storage systems, e.g. from the community
data space to your private data space. The `files-import` command can be used
for that purpose.

```
% files-import -S data-tacc-work-username \
               -U 'agave://data-sd2e-community/public_data.dat' \
               sd2e-data/
```

With the above syntax, the file located at the root directory on the
`data-sd2e-community` STORAGE system will be imported to your private STORAGE
system, and placed in your directory `sd2e-data/`.

Please also note that even though you are *able* to import files from other
Agave STORAGE systems, you may not always *need* to import those files. Most
applications of Agave will allow you to provide a complete URI path to the file,
e.g. `agave://data-sd2e-community/public_data.dat`. This is useful, for example,
in the case of large reference libraries. Pointing to the remote libraries
rather than copying them saves time and disk space.

<br>
#### Import files from the web

You can also import files from the web using the URL. This is useful
to import files that are not part of an Agave storage system:

```
% files-import -S data-tacc-work-username \
               -U 'https://raw.githubusercontent.com/sd2e/sd2e-cli/master/sd2e-cloud-cli.tgz' \
               -W 'username@email.edu' \
               sd2e-data/
```

The `-W` flag demonstrates a feature that sends an e-mail when the transfer is
complete.

<br>
#### Get help

Reminder: at any time, you can issue an Agave command with the `-h` flag to
find more information on the function and usage of the command. Extensive Agave
documentation can be found [here](http://developer.agaveapi.co/).


---
Return to the [API Documentation Overview](../index.md)
