# Select & Prepare Platform

![](../images/breadcrumbs.jpg)

## Creating PNDA image

Deploying PNDA using Heat templates requires an image with some pre-installed elements, such as `os-collect-config`. 

PNDA currently runs on CentOS/RHEL, but you can use Ubuntu or CentOS/RHEL OSes to create the PNDA image.

### Pre-requisites

**Important:** these dependencies install correctly on an Ubuntu 14.04 *server* image but fail on a *desktop* images.

If you are on Ubuntu:
```
sudo apt-get -y install python-pip python-dev qemu-utils libguestfs-tools
```

If you are on CentOS/RHEL:
```
sudo yum install epel-release
sudo yum install python-pip python-devel libguestfs-tools
```

### Setting up a virtualenv

Install virtualenv:

```
pip install --user virtualenv
```

Create the virtual environment:

```
virtualenv /path/to/project/pnda-dib
. /path/to/project/pnda-dib/bin/activate
```

### Getting the required elements

Update submodules:
```
git submodule init
git submodule update
```

Install `openstack/diskimage-builder`:

```
cd dib-utils
python setup.py install
cd ..
cd diskimage-builder
python setup.py install
cd ..
pip install six
pip install PyYAML
```

Set up environment variables, assuming you currently are in this repository's project directory (there is at least a elements directory present):


```
cat > dib_env.sh <<EOF
export ELEMENTS_PATH=tripleo-image-elements/elements:heat-templates/hot/software-config/elements:elements
export BASE_ELEMENTS="centos"
export DIB_RELEASE=trusty

# MANDATORY ELEMENTS FOR PNDA PROVISIONING
export AGENT_ELEMENTS="os-collect-config os-refresh-config os-apply-config"

# MANDATORY ELEMENTS FOR PNDA PROVISIONING
export DEPLOYMENT_BASE_ELEMENTS="heat-config heat-config-script"

# NON MANDATORY ELEMENTS FOR PNDA PROVISIONING
# but might be helpful if you plan to use anible, saltstack or puppet
# export DEPLOYMENT_TOOL="heat-config-ansible heat-config-salt heat-config-puppet"
# PNDA ELEMENTS

# USE AN ALTERNATE UBUNTU MIRROR
# export DIB_DISTRIBUTION_MIRROR="http://[MIRRORIP]/centos"

# You can specify other pnda-specific elements in the PNDA_ELEMENTS variable:
# for example, 'pnda-disable-ipv6', 'pnda-bond0' or 'os-hardening'
# Please look inside the elements directory

# If you are using 'os-hardening' element uncomment the following line
# export ANSIBLE_VERSION=2.2.1.0

export PNDA_ELEMENTS="cloud-init-pnda"
export IMAGE_NAME=pnda-image
export ALL_ELEMENTS="\$BASE_ELEMENTS \$AGENT_ELEMENTS \$DEPLOYMENT_BASE_ELEMENTS \$DEPLOYMENT_TOOL \$PNDA_ELEMENTS"

EOF
. dib_env.sh
```

### Build the image

```
disk-image-create vm $ALL_ELEMENTS -o $IMAGE_NAME.qcow2
```

### Upload the image to Glance

Install the glance client:

```
pip install python-glanceclient
```

Upload the image to the OpenStack image service:

```
. your_openstack_rc.sh
glance image-create --name pnda-base --file pnda-image.qcow2 --progress --disk-format qcow2 --container-format bare
```


# [Next](MIRROR.md)

| [Home](../OVERVIEW.md) | [Prepare](PREPARE.md) | [Image](IMAGE.md) | [Mirror](MIRROR.md) | [Build](BUILD.md) | [Stage](STAGE.md) | [Configure](CONFIGURE.md) | [Create](CREATE.md) | 
| --- | --- | --- | --- | --- | --- | --- | --- | 
