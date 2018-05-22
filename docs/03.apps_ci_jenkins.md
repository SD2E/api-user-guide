---
layout: page
title: Continuous Integration with Jenkins
tagline:
---

Jenkins is an automation server for running continuous integration tests. The
SD2E project uses [Jenkins](http://jenkins.sd2e.org/) for continuous integration
(CI) to ensure any changes that have been made to apps do not break their core
functionality. It is now standard practice to set up CI for all Agave apps. This
will ensure your deployed app is always up to date with the master branch of
your git repo, and it will alert you if jobs are not working. This guide will
help you integrate CI testing into any app you would like to deploy, but is not
meant to be a replacement for the
[Jenkins documentation](https://jenkins.io/doc/).



<br>
#### The Jenkins file

The Jenkins file defines the stages and environment variables of your Jenkins
job. The Jenkins file is written in [Groovy](http://groovy-lang.org/), and
should be located at the top-level directory for your app. For the previous
FastQC app example, the Jenkins file would be in the `~/fastqc-app/` directory.
Here is an example of a Jenkins file for the FastQC app:

```
#!groovy

pipeline {
    agent any
    environment {
        AGAVE_JOB_TIMEOUT = 900
        AGAVE_JOB_GET_DIR = "job_output"
        AGAVE_DATA_URI    = "agave://data-sd2e-community/sample/sailfish/test/read1.fastq"
        CONTAINER_REPO    = "fastqc"
        CONTAINER_TAG     = "test"
        AGAVE_CACHE_DIR   = "${HOME}/credentials_cache/${JOB_BASE_NAME}"
        AGAVE_JSON_PARSER = "jq"
        AGAVE_TENANTID    = "sd2e"
        AGAVE_APISERVER   = "https://api.sd2e.org"
        AGAVE_USERNAME    = "sd2etest"
        AGAVE_PASSWORD    = credentials('sd2etest-tacc-password')
        REGISTRY_USERNAME = "sd2etest"
        REGISTRY_PASSWORD = credentials('sd2etest-dockerhub-password')
        REGISTRY_ORG      = credentials('sd2etest-dockerhub-org')
        PATH = "${HOME}/bin:${HOME}/sd2e-cloud-cli/bin:${env.PATH}"
        }
    stages {

        stage('Create Oauth client') {
            steps {
                sh "make-session-client ${JOB_BASE_NAME} ${JOB_BASE_NAME}-${BUILD_ID}"
            }
        }
        stage('Build container') {
            steps {
                sh "apps-build-container -O ${REGISTRY_USERNAME} --image ${CONTAINER_REPO} --tag ${CONTAINER_TAG}"
            }
        }
        stage('Deploy to TACC.cloud') {
            steps {
                sh "apps-deploy -T -O ${REGISTRY_USERNAME} --image ${CONTAINER_REPO} --tag ${CONTAINER_TAG}"
                sh "cat deploy-*"
            }
        }
        stage('Run a test job') {
            steps {
                sh "run-test-job deploy-${AGAVE_USERNAME}-job.json ${AGAVE_JOB_TIMEOUT}"
                sh "get-test-job-outputs deploy-${AGAVE_USERNAME}-job.json.jobid ${AGAVE_JOB_GET_DIR}"
            }
        }
        stage('Validate results') {
            steps {
                sh "python -m pytest tests/validate-job --job-directory ${AGAVE_JOB_GET_DIR}"
            }
        }
    }
    post {
        always {
           sh "delete-session-client ${JOB_BASE_NAME} ${JOB_BASE_NAME}-${BUILD_ID}"
        }
        success {
           deleteDir()
        }
    }
}

```

Copy and paste the above text into a file called `Jenkinsfile` in the top level
of your `~/fastqc-app/` directory.

The file is divided into three sections: `environment`, `stages`, and `post`.
The `environment` section defines environment variables needed by the Jenkins
server to run the test job. Most of the variables in this section are specific
to the Jenkins server and should be left alone. The `CONTAINER_REPO` and
`CONTAINER_TAG` variables should, collectively, point to a "test" repository
location so that the versioned app is not overwritten. It is good practice to
use the app name (e.g. "`fastqc`") as the `CONTAINER_REPO`, and "`test`" as the
`CONTAINER_TAG`.

You may also need to change `AGAVE_DATA_URI` if your data is located on some
other system or in a different path. Also note that if your test data is located
in your private storage system, `data-tacc-work-username`, you will need to grant
`READ` access to the `sd2etest` user with the following command:

```
systems-roles-addupdate -u sd2etest -r USER data-tacc-work-username
```

The `stages` section of the Jenkins file will also remain largely unchanged. You
will typically need the following sections:
1. `Create Oauth client`
2. `Build container`
3. `Deploy to TACC.cloud`
4. `Run a test job`, and
5. `Validate results`

The first four steps depend on scripts that exist on the Jenkins server, and
should work the same for most apps. The final step must be written by the app
developer, and will be different from app to app. More details on this step are
included in the next section below.

Finally, the `post` section uses one more script on the Jenkins server to clean
up the session and exit the Jenkins test. This section should not change.

<br>
#### Validating results with pytests

To validate your results, you will need to define pytests that will be run to
verify your app is functional. For the FastQC app, navigate to the tests
directory `~/fastqc-app/tests`, and create a new sub-directory called
`validate-job`:
```
cd ~/fastqc-app/tests
mkdir validate-job
cd validate-job
```

Create two pytests in that directory, `conftest.py` and `test_files.py`. You can
copy and paste these two examples directly:

`conftest.py`:
```
import pytest

def pytest_addoption(parser):
    parser.addoption("--job-directory", action="store", default="job_output",
                     help="Directory containing output to evaluate")

@pytest.fixture
def job_directory(request):
    return request.config.getoption("--job-directory")
```

`test_files.py`:
```
'''Test for specific files existence in a directory'''
import pytest
import os

'''Parameterize the test with a list of required files'''
@pytest.mark.parametrize("file_list", [
    (['reads1_fastqc.html', 'reads1_fastqc.zip'])
])
def test_files(job_directory,file_list):
    '''checks job_directory for existence of all contents of file_list'''
    # Existence
    listdir = os.listdir(job_directory)
    assert(len(list(set(listdir) & set(file_list))) == len(file_list)), \
        "Missing files"
    # Files are readable and not zero length
    for f in file_list:
        try:
            fstat = os.stat(os.path.join(job_directory, f))
            assert (fstat.st_size > 0), "Zero length file: {}".format(f)
        except Exception:
            raise IOError("Couldn't stat {}".format(f))
```

The `conftest.py` adds a `--job-directory` option to pytest, this points to the
location of the output files that are created by running your app. The pytest
`test_files.py` checks if files exist and are greater than 0 bytes.

If you are creating pytests for a different app, you can simply change the output
file names in line 7 of `test_files.py`, to the file names that are output by your app:

```
'''Parameterize the test with a list of required files'''
@pytest.mark.parametrize("file_list", [
    (['reads1_fastqc.html', 'reads1_fastqc.zip'])
])
```
In this example, Jenkins is testing for the existence of `reads1_fastqc.html`
and `reads1_fastqc.zip`, which indicate that the FastQC app ran successfully.

<br>
#### Setting up the Jenkins server

Now that you have created a groovy file and defined pytests, you will need to
add your repo to the Jenkins server and define when it should run tests. We
typically have the server run a test every time a changed is pushed to the
master branch, or anytime a merge request to the master branch is made. It may
also be a good idea to schedule weekly or monthly builds to ensure your app
continues working even when no changes have been made to the source repo.

To set up Jenkins tests for your app, follow the instructions in this video tutorial:

<iframe width="560" height="315" src="https://www.youtube.com/embed/XfhgGZ0CAPw" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>


---
Return to the [API Documentation Overview](../index.md)
