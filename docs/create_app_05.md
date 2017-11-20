---
layout: page
title: Create Custom Applications
tagline: Best practices and next steps
---

Congratulations! You built and deployed your own Agave app on the SD2E tenant.
Consider the following best practices when building app bundles, and share your
work with others in the following ways:


<br>
#### Best practices for developing containerized app bundles

The app development approach demonstrated here is meant to be *flexible*, in that
it can adapt to many different scientific applications and data sets, and
*scalable*, in that it can run efficiently on TACC peta-scale systems. Some
tips for the app developer in building new apps:

1. Write a clean Dockerfile so there is no question of source code / version provenance. Minimize image size where possible by removing, e.g. source code tarballs and installation directories.
2. Design a robust, but small and portable test case to package with the app bundle. Make liberal use of error checking in `tester.sh` and `runner_template.sh`.
3. Use only command line arguments when calling the containerized executable (with the `container_exec` function). If the executable requires a configuration file, use a wrapper script inside the container to parse inputs from the command line and generate the appropriate configuration file.
4. Explicitly declare all inputs, and explicitly write all outputs. This includes file name and full path.
5. Package and curate outputs into a user-friendly format. Some use cases may benefit from a tarball of all output files; some use cases may benefit from individual files.
6. Make output file names deterministic and predictable to facilitate scripting and job chaining.
7. Document all expected outputs in the `tester.sh` and `runner-template.sh` wrapper scripts. Where appropriate, validate output and provide helpful error messaging.
8. Share your Docker images and app bundles with the SD2E community to benefit others and elicit feedback.

*Best practices were adapted from the [Computational Genomics Lab](https://toil.readthedocs.io/en/3.12.0/developingWorkflows/developing.html#best-practices-for-dockerizing-toil-workflows).*


<br>
#### Contribute your app to the SD2E github repo

Others may be interested to see your app bundle in the SD2E github repo. If you 
would like to contribute, create a pull request to the repository here:

<https://github.com/SD2E/reactors-etl>

Make your best effort to follow the same conventions (directory structure, naming
scheme, etc.) as the other apps in that repo. Also, carefully consider the size
of your app bundle. Github does not deal well with large source files and example
data, so if you can keep your app bundle to a few MB, that would be ideal.


<br> 
#### Contribute your image to the SD2E Docker hub

The SD2E Docker hub contains the run-time images of many packages that can be 
run as apps in the SD2E tenant. If you would like to commit your image to
the SD2E Docker hub, send an e-mail to `support-at-sd2e-dot-org`.

<https://hub.docker.com/u/sd2e/>

---
Go back to [Create Custom Applications](create_app.md)

Return to the [API Documentation Overview](../index.md)
