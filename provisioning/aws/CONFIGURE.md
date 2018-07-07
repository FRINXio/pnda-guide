# Configure PNDA creation process

![](../images/breadcrumbs-cfg.jpg)

## Introduction

The PNDA creation process is controlled primarily via a YAML configuration file.

A template YAML configuration can be found in the [pnda-cli repository](https://github.com/pndaproject/pnda-cli). 

## Configure pnda_env.yaml

#### Designate client machine

Create or designate a suitable machine for running the PNDA CLI. We recommend CentOS 7.

#### Obtain code

Clone the [pnda-cli repository](https://github.com/pndaproject/pnda-cli) repository from the master branch at a specific release tag (e.g. ```release/3.5```) to the client machine.

Copy ```pnda_env_example.yaml``` to create ```pnda_env.yaml```

#### Set image

Set the following image related fields as below.

| Field | RHEL | CentOS |
| --- | --- | --- |
|  `cloud_formation_parameters.imageId`  | Select 64 bit RHEL 7 image for region | Select 64 bit CentOS 7 image for region |
|  `ec2_access.OS_USER`  | ec2-user | centos | 

#### Set ec2 access keys

Set `ec2_access.AWS_ACCESS_KEY_ID` and `ec2_access.AWS_SECRET_ACCESS_KEY` to the credentials created for accessing ec2 and CloudFormation. 

These credentials are only stored on the client machine.

#### Hadoop distribution

Decide whether you want to run the Cloudera CDH or the Hortonworks HDP Hadoop distribution.

Set `hadoop.HADOOP_DISTRO` to either `CDH` or `HDP`.

#### Set source of SaltStack provisioning scripts

The PNDA software is installed and configured using the SaltStack code found in the [platform-salt](https://github.com/pndaproject/platform-salt) repository.  This can be supplied in two main ways.

##### Local copy

A local copy of platform-salt can be used by setting `platform_salt.PLATFORM_SALT_LOCAL` to the path to the platform-salt folder on the client machine.

##### Git repository

Set `platform_salt.PLATFORM_GIT_REPO_URI` and `platform_salt.PLATFORM_GIT_BRANCH` to clone a remote git URI at the specified branch during provisioning.
  
If authenticated access to `platform_salt.PLATFORM_GIT_REPO_URI` is required then place the ssh key to use, named git.pem, in the top level directory of this repository and set `platform_salt.PLATFORM_GIT_REPO_HOST` to the hostname of the server.

**Note** Please ensure that the local clone of platform-salt or  `platform_salt.PLATFORM_GIT_BRANCH` correspond to the same release tag as the pnda-cli repository cloned above.

#### Object storage

Set `pnda_application_repo.PNDA_APPS_CONTAINER` to the Application container configured during the preparation phase.

Set `pnda_application_repo.PNDA_APPS_FOLDER` to the Application folder configured during the preparation phase.

Set `pnda_data_archive.PNDA_ARCHIVE_CONTAINER` to the Dataset archive container configured during the preparation phase.

Set   `pnda_application_repo.PNDA_APPS_ACCESS_KEY_ID` and `pnda_application_repo.PNDA_APPS_SECRET_ACCESS_KEY` to the credentials created for object storage during the preparation phase.

#### PNDA mirror

Set `mirrors.PNDA_MIRROR` to the URI determined by the placement of the mirror and build components in the staging phase.

#### Other fields

There are a wide range of parameters that can be set, please refer to ```pnda_env_example.yaml``` in the [pnda-cli repository](https://github.com/pndaproject/pnda-cli) for more details.

## Security Material

#### Perimeter security (FQDN's and associated certificates/private keys)
Access to the PNDA cluster requires user authentication over a secure connection. In order to secure this user authentication, the perimeter servers require certification material which allows validating the FQDN used to access those servers to further authenticate and secure the connection to those servers.

For PRODUCTION ENVIRONMENTS, this security material MUST be generated outside the PNDA realm and dropped under the ./platform-certificates directory tree. Consult the README files under that same directory and sub-directories for further details on the required material.

For NON-PRODUCTION ENVIRONMENTS, a helper tool (./tools/gen-certs.py) is provided that can auto-generate the required server certificates based on an existing CA (private key) or based on a newly generated CA (when no private key is detected in the ./platform-certificates directory by the helper tool).

#### SSH key pair

Create [an ssh keypair](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html) to use when creating the ec2 instances for PNDA as ```key_name```. 

Place the private key ```key_name.pem``` in the root of the pnda-cli directory. 

Ensure that key_name.pem has 0600 permissions. 

For example, if the keypair in AWS is 'pnda' then the local private key file should be named 'pnda.pem'.

# [Next](CREATE.md)

| [Home](../OVERVIEW.md) | [Prepare](PREPARE.md) | [Mirror](MIRROR.md) | [Build](BUILD.md) | [Stage](STAGE.md) | [Configure](CONFIGURE.md) | [Create](CREATE.md) | 
| --- | --- | --- | --- | --- | --- | --- |
