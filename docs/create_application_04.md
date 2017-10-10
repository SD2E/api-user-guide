---
layout: page
title: Create Custom Applications
tagline: Add your app to the SD2E tenant
---

The final step required before you can run the app is to edit the `json` file
descriptions so your app is compatible with Agave.
```
fastqc/
├── Dockerfile                 # done !
├── build.sh                   # done !
├── deploy.sh
├── example                    # done !
│   ├── SP1.fq                 # done !
│   ├── SP1_fastqc.html        # done ! (can remove)
│   └── SP1_fastqc.zip         # done ! (can remove)
├── fastqc-0.11.5
│   ├── _util                  # done ! (did not change)
│   │   └── container_exec.sh  # done ! (did not change)
│   ├── app.json
│   ├── app.yml
│   ├── job.json
│   ├── runner-template.sh     # done !
│   └── tester.sh              # done !
└── src                        # done !
    ├── README.md              # done !
    └── fastqc_v0.11.5.zip     # done ! (optional)
```

<br>
#### Write the app description

The Agave app description is contained in `app.json`.


The YAML file (`app.yml`) is probably not strictly necessary. It is a clone of
the `app.json` file, just in a different format.


<br>
#### Write a job template

Instructions for running an instance of an app (a job), are located in the 
`job.json` file.


<br>
#### Inspect and run the deploy script

```
% bash deploy.sh
```


<br>
#### Run a test job

```
% jobs-submit -F job.json
```

---
Proceed to [Next steps](create_application_05.md)

Go back to [Create Custom Applications](create_application.md)

Return to the [API Documentation Overview](../index.md)
