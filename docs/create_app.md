---
layout: page
title: Create Custom Applications
tagline:
---

You can find Agave applications ("apps") in the SD2E catalog by using the `apps-list`
command described [previously](find_application.md). What if the app you are
looking for is not available? Using the Agave CLI, you can create your own.

<br> 
#### Components of an app

The essential components you need to create your own app are:

1. A Docker image containing the executable and all runtime dependencies
2. A wrapper script (generally written in bash) that runs the executable
3. A test script (also written in bash) for testing the executable outside of the Agave environment

Several example apps exist and all components are visible. For example, visit the
[SD2E Docker hub](https://hub.docker.com/u/sd2e/) to view existing app containers.
And, example app bundles can be viewed on the
[SD2E github page](https://github.com/SD2E/reactors-etl/tree/master/reactors).

<br>
#### Create your own by example

The best way to demonstrate the creation of a custom app is by example. The 
following sub-pages will go through the process:

1. [Clone an example app](create_app_01.md)
2. [Containerize your executable](create_app_02.md)
3. [Build wrapper and test scripts](create_app_03.md)
4. [Add your app to the SD2E tenant](create_app_04.md)
5. [Best practices and next steps](create_app_05.md)


---
Return to the [API Documentation Overview](../index.md)
