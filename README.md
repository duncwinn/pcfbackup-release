# PCF Backup BOSH Release

This is currently a shell of a [BOSH](http://bosh.io) release for running
PCF backups using cfops. Most of what is here provides examples at this
point rather than what we'll actually use (e.g. doesn't distinguish between
different types of backups). The empty monit file allows the job to start up
with out actually running anything for now and then you can ```bosh ssh```
into it for development purposes. You can also see how property substitution
works with deployments.  

## Running Locally

### Install BOSH Lite

Install BOSH Lite using the instructions at
https://github.com/cloudfoundry/bosh-lite. You do not need to install Cloud
Foundry.  

### Deploy PCF Backup Release

#### Upload the Warden stemcell

Download latest Warden stemcell:

 ```
 wget http://bosh-jenkins-artifacts.s3.amazonaws.com/bosh-stemcell/warden/latest-bosh-stemcell-warden.tgz
 ```

 Upload the stemcell:

 ```
 bosh upload stemcell latest-bosh-stemcell-warden.tgz
 ```

#### Populate the blobs directory

The blobs aren't published yet to a blobstore so we need to populate our
local blobs directory. It should look like the following:  

```
$ tree blobs/
blobs/
├── cfops_linux_amd64
│   └── cfops_linux_amd64-0.0.30.tar.gz
├── openjdk-1.8.0_25
│   └── openjdk-1.8.0_25.tar.gz
└── s3-cli-0.1.0
    └── s3-cli-0.1.0-package.tar.gz

3 directories, 3 files
```

Download and add dependencies:
* [cfops_linux_amd64.tar.gz](http://s3.amazonaws.com/pivotal-cfops/release/v1.0.0/cfops_linux_amd64.tar.gz)
```
wget https://github.com/pivotalservices/cfops/releases/download/v1.0.0/cfops_linux_amd64.tar.gz -O cfops_linux_amd64-1.0.0.tar.gz
bosh add blob cfops_linux_amd64-1.0.0.tar.gz cfops_linux_amd64
```
* [Python-2.7.8.tgz](https://www.python.org/ftp/python/2.7.8/Python-2.7.8.tgz)
```
wget https://www.python.org/ftp/python/2.7.8/Python-2.7.8.tgz
bosh add blob Python-2.7.8.tgz python
```
* [awscli-bundle.zip](https://s3.amazonaws.com/aws-cli/awscli-bundle.zip)
```
curl "https://s3.amazonaws.com/aws-cli/awscli-bundle.zip" -o "awscli-bundle.zip"
bosh add blob awscli-bundle.zip aws-cli
```

#### Upload the PCF Backup release

Create a dev release:

```
bosh create release --force
```

Upload the release:

```
bosh upload release
```

#### Generate a deployment manifest

Note: we don't have a manifest generation yet so skip this

```
bosh-lite/make_manifest
```

#### Deploy

Target the generated deployment manifest

```
bosh deployment manifests/pcfbackup-warden.yml
```

Deploy

Make sure you update the director_uuid in manifests/pcfbackup-warden.yml to
match your local director's uuid (run bosh status to see details).  

```
bosh -n deploy
```

## Create a new dev releases

http://bosh.io/docs/create-release.html provides full details or make some
changes and just follow these steps:  

* Create the dev release

```
bosh create release --force
```

* Upload the release

```
bosh upload release
```

* Deploy the release

```
bosh -n deploy
```
