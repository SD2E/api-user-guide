---
layout: page
title: "Data Management: Share and Import"
tagline:
---

The Agave CLI makes it possible to share data with other users on the SD2E
tenant, and import data from the web. Use your best judgement in deciding
whether to copy shared data or link against shared data with the understanding
that storage space is limited.

This page is broken down into the following sections:

1. [Find another user](#find-another-user)
2. [Share files with another user](#share-files-with-another-user)
3. [Share files using postits](#share-files-using-postits)
4. [Import files from other systems](#import-files-from-other-systems)
5. [Import files from the web](#import-files-from-the-web)
6. [Further help](#further-help)


<br>
#### Find another user
To share a file with another user on the SD2E tenant, you must first know their
username. The Agave CLI has a set of tools that can be used to find other users.
View your own user profile by issuing:

```
% profiles-list -v me
{
  "first_name": "Joe",
  "last_name": "User",
  "full_name": "juser",
  "email": "juser@example.org",
  "phone": "",
  "mobile_phone": "",
  "status": "",
  "create_time": "20150223162108Z", 
  "username": "juser"
}
```

Each of the fields stored in the JSON object is queryable using the profiles-search
command. Some more common examples include:
```
# Search for another user by first name
% profiles-search --rich 'first_name=Joe'

# Search for another user by last name
% profiles-search --rich 'last_name=User'

# Search for another user by e-mail address
% profiles-search --rich 'email=juser@example.org'

# Search for another user by guessing all or part of their username
% profiles-search --rich 'username.like=*juser*'
```

The `--rich` flag summarizes part of the JSON response in a human-friendly table.

Note: There is a file called 'richtext' in the CLI bin directory that needs to
be updated for `profiles-search`. Fields were changed as some point from `firstName`
and `lastName` to `first_name` and `last_name`. Also, `institution` is no longer
a field so it must be removed.

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
username READ WRITE EXECUTE
vaughn READ WRITE EXECUTE
```

To add permissions for another user to view the file, use the `files-pems-update`
command:
```
% files-pems-update -U collaborator -P ALL sd2e-data/my_file.txt
% files-pems-list -S data-tacc-work-username sd2e-data/my_file.txt

collaborator READ WRITE EXECUTE
sd2e READ WRITE EXECUTE
username READ WRITE EXECUTE
vaughn READ WRITE EXECUTE
```

Now, a user with username `collaborator` has permissions to access the file.
Valid values for setting permission with the `-P` flag are READ, WRITE, EXECUTE,
READ_WRITE, READ_EXECUTE, WRITE_EXECUTE, ALL, and NONE. This same action can be
performed recursively on directories using the `-R` flag.

Next, the collaborator needs the correct permissions to access your STORAGE
system. To see who has access to your STORAGE system, perform:
```
% systems-roles-list data-tacc-work-username
vaughn OWNER
sd2e ADMIN
username ADMIN
```

To add your collaborator to your system, use the `systems-roles-addupdate` command:
```
% systems-roles-addupdate -u collaborator -r GUEST data-tacc-work-username 
Successfully updated roles for user collaborator on data-tacc-work-username

% systems-roles-list data-tacc-work-username
vaughn OWNER
collaborator GUEST
sd2e ADMIN
username ADMIN
```

Now, a user with username `collaborator` can see files with the appropriate
permissions on your STORAGE system. Valid values for setting a role with the `-r` 
flag are GUEST, USER, PUBLISHER, ADMIN, and OWNER.

Finally, ask your collaborator to download the file with the exact same command
you use to download the file:
```
% files-get -S data-tacc-work-username sd2e-data/my_file.txt
```


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
#### Further help

Reminder: at any time, you can issue an Agave command with the `-h` flag to
find more information on the function and usage of the command. Extensive Agave
documentation can be found [here](https://tacc.github.io/developer.tacc.cloud/).


---
Return to the [API Documentation Overview](../index.md)
