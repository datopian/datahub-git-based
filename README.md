A README-based demo of next generation, fully-git(hub) based DataHub. We intend this to be https://next.datahub.io/

To start with let's KISS: fully GitHub based so GitHub provides MetaStore + HubStore

Here's a walk through of the desired experience. We walk through 3 cases:

* "Small" data files -- these are stored in git.
* "Big" data files (> a few Mb) -- these are stored via git-lfs + giftless into your cloud of choice
* "Dependencies" -- I want to use external data in my project that I don't manage or want to store directly **not specced yet**

# Small Data Example

## The Dataset

Suppose you have a dataset on your hard disk like:

```
my-dataset/
  datapackage.json
  mydata.csv
  README.md
```

`datapackage.json` contents:

```json=
{
  "name": "my-dataset",
  "title" "My awesome dataset",
  "resources": [
    {
      "name": "mydata",
      "path": "mydata.csv"
    }
  ]
}
```

`mydata.csv`

```
A,B,C
1,2,3
```

`README.md`

```console=
$ echo "This is my awesome dataset. Check it out now!" > README.md
```

## Pushing a Dataset

```console=
$ cd my-dataset
$ git init
$ git add *
$ git commit "My new dataset"
$ git push https://github.com/myorg/my-dataset
```

If we added lfs setup we need an lfsinfo:

```
echo "..." > .lfsinfo
```

## Then on DataHub.io

Showcase page:

`$ open https://datahub.io/@myorg/my-dataset/`


# Big(ger) data ... (too big on git)

Let's modify our previous example to have a large file:

```
my-dataset/
  datapackage.json
  mybigdata.csv
  README.md
```

`datapackage.json` contents:

```json=
{
  "name": "my-dataset",
  "title" "My awesome dataset",
  "resources": [
    {
      "name": "mybigdata",
      "path": "mybigdata.csv"
    }
  ]
}
```

## Add support for storing large data in the cloud

Set a custom LFS server that will then hand out credentials for you to store the file in the cloud

```
# see https://github.com/git-lfs/git-lfs/wiki/Tutorial#lfs-url
# this uses the default datahub.io provided cloud storage
$ git config -f .lfsconfig lfs.url https://giftless.datahub.io/
$ git add .lfsconfig
$ git commit -m "Setting up custom storage for my big data files on datahub.io storage"
```

:::info
It would be really cool to allow people to bring their own storage if they wanted e.g. `lfs.url` is

```
https://giftless.datahub.io/s3/mybucket/
```
:::

## Push the Dataset

```console=
$ git lfs track mybigdata.csv
$ git add *
$ git commit "My new dataset"
$ git push https://github.com/myorg/my-dataset
```

# API interaction

NB: these API examples are more based on classic DataHub API setup rather than pure git(hub) based. However, wanted to include them for reference and for the future.

## Getting Started (as a client)

You can access the Web API using any HTTP client, for example `curl`. 

## Auth Token

First, you must obtain a signed access token. For example, using a token signed by `ckanext-authz-service` and the CKAN 2.x API. In the following examples,  we'll assume this token is saved as `$API_TOKEN`.

You will add to every request an authorization header like:

```
curl -H "Authorization: Bearer $API_TOKEN"
```

We will omit this header in the examples that follow for the sake of simplicity. But please add it back in.

## Creating a dataset

A basic sequence

```
# create your project myorg/mydataset
curl -X POST https://datahub.io/api/projects/myorg%2Fmydataset

# you have an empty dataset!
curl -X GET https://myckan.org/api/projects/:id/dataset
{
  // TODO: is name without @ character
  name: '@myorg/mydataset',
  id: 'unique-identifier'
}

curl -X PUT https://myckan.org/api/projects/:id/dataset
{
  'title': 'My new title'  
}

curl -X GET https://myckan.org/api/projects/:id/dataset/revisions
```

Another way to push a file (not in LFS):

```
curl -X PUT https://myckan.org/api/projects/:id/resources/data%2Fdata.csv -d @data.csv
```

The server will take care of adding some metadata to `datapackage.json` pointing to this file and saving this file to storage. 

## Access a dataset that is owned by an org I'm a member of

2. List the organizations by calling the org list endpoint in the "hub API"
3. Pick an org I want to access
4. List datasets owned by this org by calling the dataset search API endpoint
5. Get an authz token to read the dataset and resources I want to access from the authz endpoint
6. Get the selected dataset metadata from the metastore service (this is what *metastore-service* is about)
7. Get the resources pointed out by this dataset from blob storage service (currently `giftless`)

Ideally, over time, all of these endpoints will be behind the same API gateway, consume the same identity / authorization tokens, etc. 


# What is the MVP plan?

Outcome visioning: I can do either the paths above and view my dataset on next.datahub.io

```mermaid
graph TD

start[Start]
push[Push small & big dataset]
showcase[View a dataset showcase]

start --> push
start --> showcase

push --> workflows[Workflows]
```


* [ ] Push
  * [x] Push "small" data **working today pretty much 😉 - the only thing beyond basic git is adding datapackage.json which you literally do "by hand" for now**
  * [ ] Push flow works with big files to the cloud
    * [ ] Deploy a giftless server https://github.com/datopian/giftless
      * [ ] Have a cloud storage provider and e.g. a bucket
      * [ ] Have giftless backend for this
    * [ ] Push my dataset (as per above)
    * [ ] Verify it worked (someone else can clone!)
      * [ ] What about auth? **Let's make giftless hand out tokens all the time atm ...**
* [ ] Showcase: showcase a dataset i.e. i can visit datahub.io/github.com/datasets/my-small-test-dataset/ and ditto for big dataset and it looks something like datahub.io/core/finance-vix today ...
  * [ ] Choose a JS based frontend framework
  * [ ] Mock out the page
  * [ ] Wire it up
    * [ ] Write a backend client (and abstraction) library e.g. `getDatasetMetadata(identifier, ref='master'), getFileUrl(dataset, ...)`
  * [ ] Deploy
* [ ] Workflows: build workflows that are triggered on each change or other times e.g. "Derived data / alternate formats" (build me zip, build me json for this csv), data validation, ...
  * [ ] Abuse github workflows for now ...
    * [ ] Have a pattern and core library for this ..
  * [ ] Plan out how to move to cloud based airflow or Beam or similar ...
  * [ ] Monitoring and reporting (e.g. minutes used, what's failing etc)
    * [ ] Integrate into a user dashboard on datahub.io
