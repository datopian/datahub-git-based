# DESIGN

## Design choices 

* [KISS] We are using Github as *both* MetaStore and HubStore to start with

## Domain Model

* Dataset
* Resource (or File)
* Revision
* Tag

## Actions / Flows

* View Dataset: [Showcase page] a page displaying the dataset (or a resource)
  * View a Revision / Tag / Branch:
* Add / Upload: ...
* Tag


## Components

* MetaStore: stores dataset metadata (and revisions)
* HubStore
* SearchStore
* BlobStore: stores


## Spec for data structure (unfinished)

Data in git has the following files:

```
datapackage.json
.lfsinfo
.gitattributes
```

`.gitattributes`

```
mybigdata.csv filter=lfs diff=lfs merge=lfs -text
```

### How does this work in mixed case of some data in git, some cloud (via git-lfs)?

Say we have this as our structure:

```
# datapackage.json
{
  name: 'my-dataset',
  resources: [
    {
      "name": "mydata",
      "path": "mydata.csv"      # stored in the git repo using git protocol ...
    },
    {
      "name": "mybigdata",
      "path": "mybigdata.csv"   # pointer stored in the git repo using git-lfs protocol .
                                # actual file in cloud storage    ..
    },
    {
      "name": "myremotedata",
      "path": "https://mysite.com/thedata.csv"  # stored online somewhere
    }
  ]
}

# mydata.csv
A,B,C
1,2,3

# mybigdata.csv - stored in cloud storage
version https://git-lfs.github.com/spec/v1
oid sha256:4d7a214614ab2935c943f9e0ff69d22eadbb8f32b1258daaa5e2ca24d17e2393
size 12345
```

Then how would this show up in frontend? TODO ...
