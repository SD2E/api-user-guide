---
layout: page
title: SSH Key Pairs
tagline:
---

In certain circumstances, it is advantageous to use public / private SSH keys
to authenticate with Agave. Specifically, this is important when
[creating your own private systems](create_systems.md). Having SSH keys will make it 
easier to interact with systems that do not allow password authentication or that
require frequent password changes, and it will increase security by preventing
the need to store your password in a plain text file.

If you are new to the Agave CLI and the SD2E Agave tenant, it is recommended to
skip this section of the documents until you need to
[create your own private systems](create_systems.md).

<br>
#### Prerequisites

For this example, we will demonstrate how to generate keys for an Atmosphere VM
instance on [Jetstream](https://jetstream-cloud.org). We need:

* Hostname / IP address for target system
* User account and login credentials for target system

<br>
#### Log in to the remote system

First, remotely log in to the system on which you plan to make a private HPC or
STORAGE system:
```
% ssh username@129.114.104.39
username@129.114.104.39's password:
Welcome to
    _   _                             _
   / \ | |_ _ __ ___   ___  ___ _ __ | |__   ___ _ __ ___
  / _ \| __| '_ ` _ \ / _ \/ __| '_ \| '_ \ / _ \ '__/ _ \
 / ___ \ |_| | | | | | (_) \__ \ |_) | | | |  __/ | |  __/
/_/   \_\__|_| |_| |_|\___/|___/ .__/|_| |_|\___|_|  \___|
                               |_|
[js-104-39] % 
```

<br>
#### Generate SSH keys

Next, generate a secure set of RSA SSH keys using the Linux utility `ssh-keygen`.
Here, we call the public / private key pair 'agave_rsa', and we only use it for
this system, and only for Agave:
```
[js-104-39] % ssh-keygen -q -t rsa -b 4096 -C user@email.com -f agave_rsa -N ''

[js-104-39] % cat agave_rsa
-----BEGIN RSA PRIVATE KEY-----
MIIJJwIBAAKCAgEA1kto/IyPDmXR4Rk/I0ubRqV/IF5haHF3MI1yBMwxKJ6d7Wr7
BDFIbFH/CiAYiMsUUier5E1/HsRwYCrmOIvWI9VAGYpHLWw6YE62Z1SiE1yatELM
5ISf22w256BLyNcgI5l5L1CEXd6ChbPF0hYiWrp5a06gggZPLwjjbyVwOqztn0mB
HkzovrhdtyGmThnjBJVmcAXylsxGKwyARmcvmJl53L3/Ci5nYTCaIr4SZwRR8108
V4/BF9NNm9VEfyI2q4/DH5/lKFvNm59V7zRZ0kSWq84BN1OxImjlS9Mzj8lLEYBT
KYouxn1FHnbhhi2hCGRMeDnopv6+Z9/4bdPdTY3TXSy9bqL+Q280Z+taydVa3BeH
+gbFBzKVltICntO5KKmmNBknL8ULPvpbeaY/yTKiCJVkOElhweoRqrzEtNMqX19q
...etc

[js-104-39] % cat agave_rsa.pub
ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAgEA1kto/IyPDmXR4Rk/I0ubRqV/IF5h
...etc
```

<br>
#### Authorize public key

Finally, add your public key to the `authorized_keys` file on your remote system:
```
[js-104-39] % cat agave_rsa.pub >> ~/.ssh/authorized_keys
```
Your private key (`agave_rsa`) will be used in the future when it is time to
[create your own private systems](create_systems.md). Never share your private
key with anyone else.


---
Return to the [API Documentation Overview](../index.md)
