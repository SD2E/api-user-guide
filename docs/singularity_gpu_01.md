---
layout: page
title: Utilizing GPUs with Singularity
tagline:
---

Utilizing GPGPUs on the Maverick supercomputer through containerized environments.

## Choosing the Right System

You can register your app to ANY system at TACC, but Maverick may not always be the best choice if you don't always need GPUs.

| System     | Cores/Node | Pros                                      | Limitations                                |
|:-----------|:----------:|:------------------------------------------|:-------------------------------------------|
| Stampede   | 16         | Thousands of nodes, Xeon Phi accelerators | Retiring ~ Dec2017                         |
| Stampede 2 Phase1 | 68         | Thousands of nodes, KNL processors | Slow for serial code                       |
| Stampede 2 Phase2 | 48         | Thousands of nodes, Skylake processors | Coming Soon, High Demand               |
| Lonestar 5 | 24         | Compute, GPUs, Large-mem | UT only, slow external network                              |
| Wrangler   | 24         | SSD Filesystem for fast I/O, Hosted Databases, Hadoop, HDFS | Low node-count           |
| Jetstream  | 24         | Long running instances, root access       | Limited storage                            |
| Maverick   | 20         | GPUs, high memory nodes                   | Deprecated software stack                 |
| Chameleon  | Variable   | GPUs, bare metal VM, software defined networking | Difficult to configure              | 
| Catapult   | 16         | FPGAs                                     | Windows-only                               |

You can learn about all choices at the [TACC Systems Overview](https://www.tacc.utexas.edu/systems/overview). Detailed specifications can be found in the *User Guide* of each system.

If you have an application already configured on a non-tacc system, you can register that system to the SD2E agave tenant.

- [System Registration Guide](https://sd2e.github.io/api-user-guide/docs/create_systems.html)

After registration, you can not only run applications, but access data as well. Just remember that applications will run as YOUR user when you share them with others.

## Containers @ TACC

TACC supports containerized compute environments through [Singularity](http://singularity.lbl.gov/), which provides environment encapsulation without privilege escalation (root). Singularity provides the following functionality:

- Environment encapsulation
- Image based containers (single file)
- Devices and interconnects are passed into container
  - Infiniband
  - GPGPUs
- No abnormal privilege escalation allowed
- No root daemons
- Containers are read-only when not root
- Pass in filesystems and directories your user has access to

Since version 2.3, Singularity has supported the two following workflows

### Local Container Development

Create a Singularity container from scratch.

1. Create image of specific size
2. (sudo) bootstrap image
   * (sudo) [add content through definition file](http://singularity.lbl.gov/archive/docs/v2-3/bootstrap-image)
   * (sudo) [manually install software](http://singularity.lbl.gov/archive/docs/v2-3/docs-changing-containers)
3. Done

<http://singularity.lbl.gov/archive/docs/v2-3/bootstrap-image>

### Docker Import

Utilize your knowledge of Docker to create Singularity images.

1. Pull docker image
2. Run docker image

<http://singularity.lbl.gov/archive/docs/v2-3/docs-docker>

### Running the container

These containers are run without root, so you simply

- run - Run the default functionality of the container, which takes in arguments
- exec - Execute a specific command inside the container, and then exit
- shell - Enter the container and interactively run commands

## GPU containers

Since Singularity supported docker containers, it has been fairly simple to utilize GPUs for machine learning code like [TensorFlow](https://www.tensorflow.org/). From Maverick, which is TACC's GPU system:

```
# Work from a compute node
idev -m 60
# Load the singularity module
module load tacc-singularity
# Pull your image
singularity pull docker://nvidia/caffe:latest
# 
singularity exec --nv caffe-latest.img caffe device_query -gpu 0
```

Please note that the `--nv` flag specifically passes the GPU drivers into the container. If you leave it out, the GPU will not be detected.

```
singularity exec caffe-latest.img caffe device_query -gpu 0
```

For TensorFlow, you can directly pull their latest GPU image and utilize it as follows.

```
# Change to your $WORK directory
cd $WORK
#Get the software
git clone https://github.com/tensorflow/models.git ~/models
# Pull the image
singularity pull docker://tensorflow/tensorflow:latest-gpu
# Run the code
singularity exec --nv tensorflow-latest-gpu.img python $HOME/models/tutorials/image/mnist/convolutional.py
```

You probably noticed that we check out the models repository into your `$HOME` directory. This is because your `$HOME` and `$WORK` directories are only available inside the container if the root folders `/home` and `/work` exist inside the container. In the case of `tensorflow-latest-gpu.img`, the `/work` directory does _not_ exist, so any files there are inaccessible to the container.

You may be thinking "what about overlayfs??". The Linux kernel on Maverick does not support overlayfs, so it had to be disabled in our singularity install.

## Build your APP

You can then use these methods in your [next Agave app](create_app.md).
