---
layout: page
title: Share an Application with Others
tagline:
---

As a normal user of the SD2E tenant, you have permissions to build and deploy
private apps only. Private apps are, by default, only visible to you. To share
an app with your colleagues, follow the steps below.


<br>
#### Update permissions on an app

Assuming you have the private app `fastqc-username-0.11.5` (developed
[elsewhere in this user guide](create_application.md)), you can check who has 
permission to access the app with the following command:
```
% apps-pems-list fastqc-username-0.11.5
username READ WRITE EXECUTE
```

(Replace `username` with your TACC username). By default, as creator of the app
you are the only with with access, and you have `+rwx` permissions.

Next, identify the TACC `username` of the user with whom you would like to share
your app. (See [this page](share_import.html#find-another-user) for help). Given
the username `my_collaborator`, grant that user permissions with:
```
% apps-pems-update -u my_collaborator -p ALL fastqc-username-0.11.5
Successfully updated permission for my_collaborator

# list permissions again
% apps-pems-list fastqc-username-0.11.5
username READ WRITE EXECUTE
my_collaborator READ WRITE EXECUTE
```

Ask your collaborator to perform the `apps-list` command, and they should now
be able to see your app.


<br> 
#### Update permissions on an execution system

Before your collaborator can run a job with your app, they must also have
correct permissions on the app execution system. If you built your app to use
one of the public execution systems (e.g. `hpc-tacc-maverick`), then no more 
changes should be necessary. If you built your app to use a private execution
system (e.g. `hpc-tacc-maverick-username`), then you must also grant your
collaborator the correct permissions on that system.

*Tip: The app execution system is listed in the app.json file, or can be found
by issuing '`apps-list -v app-name | grep executionSystem`'.*

[Elsewhere in this user guide](create_application.md) we deployed our private
app on our private exection system, `hpc-tacc-maverick-username`. Use the 
`systems-roles-list` command to see who can access it:
```
% systems-roles-list hpc-tacc-maverick-username
sd2eadm OWNER
username ADMIN
```

Grant your collaborator `USER` access with the following:
```
% systems-roles-addupdate -u my_collaborator -r USER hpc-tacc-maverick-username
Successfully updated roles for user my_collaborator on hpc-tacc-maverick-username

# list permissions again
% systems-roles-list hpc-tacc-maverick-username
sd2eadm OWNER
username ADMIN
my_collaborator USER
```

Ask your collaborator to perform the `systems-list` command, and they should now
be able to see your private system. Now, your collaborator can run jubs against
your private app using the same `job.json` file and `jobs-submit` commands as you.


<br>
#### Publish the app globaly

Normal SD2E users do not have the appropriate permissions to publish an app
globally. If you have deployed and tested
an app, and are happy with its behavior, please contact `support-at-sd2e-dot-org`
and an admin will help to publish your app.

---
Return to the [API Documentation Overview](../index.md)
