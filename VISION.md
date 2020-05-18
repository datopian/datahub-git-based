# A Vision for a Next DataHub

## Preliminaries

Always bet on git

![](https://i.imgur.com/HvNXyh7.jpg)

Rather than start from data storage and try and add versioning ... Let's start from versioning and add large file storage and workflows.

git + cloud => heaven for data scientists and engineers

## Features

* Data versioning (seamless with your existing tools)
* Present or share your work with others (quickly)
* Built in workflows (with apache airflow, google dataflow or your own)

## Product Offerings

Built on CKAN, Frictionless etc

* Enterprise: install on your own servers or cloud, or have us host a dedicated instance for you
* Team: per person pricing
* Private: private datasets just for you
* Open: free

## Design

* git(hub) backed
  * FileTree storage
  * Gives you versioning out of the box
  * familiar, highly adopted tooling `git` etc
* Add cloud storage for larger data
  * This could almnost be a product in its own right "bring your own cloud and combine with git(hub/lab/...)"
* data oriented frontend datahub.io
* dataflows: do useful stuff for people
  * README supports embedding e.g. table schema
  * file presentation works for data (no problem if my file is large etc)
  * Summary stats
  * Data Validation etc ...

Extras

* Paste data like adding an image on imgur
  * Make it insanely easy to add some data quickly for sharing etc.
  * "dist" (gist for data)
* Add patterns for "dependencies"
* Add tooling for local cache of data (this is almost independent)

MVP comments

* even use github for authorization and hubstore (ie. list of stuff)

## FAQs

### Why not just github?

b/c not designed for data e.g. previews don't work for even smallish data ...

![](https://i.imgur.com/P3XDJjg.png)


## Job Storiese

### Introduction: A Project in data science

Looks something like:

```
README
code (models)
data (sources)
```

Possibly do some data wrangling

=> I need to manage data (a bit)

### The Data matrix

|                   | Small |  Large |
|---|--|--|
| Manage / Control  |  Git  |  Git-LFS + Cloud | 
| Don't Manage ...  |  datapackage.json | datapackage.json plus local cache |

### "Pushing" (and sharing) data stories

#### I want to keep data with my (data science) project => use git + git-lfs + custom server (giftless)

* I want to do a data wrangling project and keep code and data together ...
* I want to add data to my github project ... (but it's large or external)
* I want to use my cloud of choice for my data and github for my project

AND/OR I want to version data so that it stays in sync with my code / so that i efficiently store data

Try

1. Move data to a git repo => X too big =>
2. Git-lfs => X slowish, size limitations, expensive on github, can't use my own tools (e.g. i want to push data to my cloud of choice), awkward for clients who want to give me data =>
3. custom server (giftless) so that you can use git-lfs plus cloud
    * Extra: data bridge to pull in data automatically from elsewhere
    * TODO: issue that git-lfs forces CAS which is opaque. I would like to just add my files with their names => use datapackage.json (??) - see below for data outside my project

#### I want to use data from outside from project (that I don't want to host or I don't manage)

* Use datapackage.json + a data cache (for local development)

```javascript=
resources: [
  {
    path: "s3://my-client/their-data/bigfile.csv"
  }
]
```


### Viewing / Consuming beyond raw download / Value-Added stories

#### I want to open that data from my language of choice quickly => use our custom library

> All we should be doing is opening the bytestream for them (or even just locating the file). Parsing and anything else we can leave to them.

#### I want to publish or document my dataset => use datapackage.json (+ tooling)

#### I want to share my dataset => datapackage.json + tooling and/or datahub.io

#### I want a Data API (quickly)

* Push with datapackage.json
* Then add this ... workflow in `.dataflows` directory
* Then connect to datahub.io

#### I want data stats

Connect my repo to datahub.io

#### I want data validation

Connect my repo to datahub.io (which will auto process in the background)


## Related Tooling

### `data`

JS based tool for wrangling data on CLI and with the UI.

data framework based on f11s

* inspect
* view incl visualize
* generate data package / resource
* transform

> Why would someone use this vs e.g. pandas or google spreadsheets. Seems secondary atm.

### data flows

TODO - see https://tech.datopian.com/flows/
