---
layout: page
title: Archiving to Ranch
tagline:
---

TACC's HPC systems have large `$WORK` and `$SCRATCH` directories to facilitate running
scientific applications against big data sets. However, the size of these filesystems
make it unrealistic for TACC to back up all user data. We recommend that each user
back up their own personal or project data located on `$WORK` or `$SCRATCH` file
systems. The resource we provide to facilitate this is an archival system called
'Ranch'. This guide will cover:

1. [System access](#system-access)
2. [Transferring data to Ranch](#transferring-data-to-ranch)
3. [Retrieving data from Ranch](#retrieving-data-from-ranch)


<br>
#### System access

Ranch is available from a terminal by direct login with `ssh`. Your TACC username,
password, and multifactor token will be required to login:

```
% ssh username@ranch.tacc.utexas.edu
```


<br>
#### Transferring data to Ranch

The most effective way to archive your personal data to Ranch is using the `scp`
command from a Linux terminal. For example:
```
% scp mydata ${ARCHIVER}:${ARCHIVE}/myfilepath
```

The environment variables `${ARCHIVER}` and `${ARCHIVE}` are defined on each of
TACC's production machines to automatically point to the correct Ranch host and
home directory, respectively.

Please also note that tape file systems work most efficiently with fewer large
files, rather than many small files. Please make an effort to `tar` your folders
of many thousands of small files into one large file for archiving. File sizes of
around 250GB are ideal. This can be done automatically in the transfer:
```
% tar cvf - dirname | ssh ${ARCHIVER} "cat > ${ARCHIVE}/mytarfile.tar"
```



<br>
#### Retrieving data from Ranch

If you need to retrieve data from backups, log directly into Ranch (as above)
and transfer your data back to one of the TACC HPC systems before unpacking. For
example:
```
% ssh username@ranch.tacc.utexas.edu

[ranch]% scp mytarfile.tar username@stampede2.tacc.utexas.edu:/scratch/01234/username/
[ranch]% ssh username@stampede2.tacc.utexas.edu

[stampede2]% cd /scratch/01234/username
[stampede2]% tar -xvf mytarfile.tar
```
<br><br>
For more information, please see the [Ranch User Guide](https://portal.tacc.utexas.edu/user-guides/ranch).


---
Return to the [API Documentation Overview](../index.md)
