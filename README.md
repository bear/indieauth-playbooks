## Requirements

- Python v2.7+
- Virtualenv
- Ansible (installed via pip)
- A ~/secrets/ folder where ssh keys and other things are stored that are not found in GitHub

## Assumptions

- Ubuntu LTS 14.04
- root user access
- The playbooks assume that the ```provision.yml``` playbook has been run against an Ubuntu LTS image.

## Installation

To setup a python virtual environment to run:

    virtualenv --python=python2.7 indieauth-playbooks

To "activate" the virtual environment for your current shell session:

    cd indieauth-playbooks
    . bin/activate

To install ansible within the virtual environment:

    pip install --upgrade pip
    pip install ansible

The ansible playbooks for indieauth then need to be retrieved:

    git clone http://github.com/bear/indieauth-playbooks

NOTE: The ```pip install --upgrade pip``` step needs to be only run immediately after creating a virtual environment to ensure you are running the lates version of pip.

## Configuration

Ansible uses the ```hosts``` file to control information about the hosts it will be managing.

The ```group_vars/all``` file contains global variables.

The ```roles``` folder is where common tasks are grouped into named roles along with any role specific variables or templates.

The ```secrets``` variable found within ```group_vars/all``` contains the path that will be used to lookup ssh keys and other items that will never appear in the playbook directory path. This keeps them from ever being accidently added to github.

## Playbooks

### provision.yml
Does the baseline install of an ops user and locks down the server's sshd, install iptables and a set of scripts to manage iptables. It also creates the indieauth user and installs/configures anything required for use later with the ```indieauth.yml``` playbook.

    ansible-playbook -i hosts provision.yml --limit indieauth-nyc

NOTE: the ```--limit``` option is used to limit to a single host which is not required but can speed things up when you get to having a lot of hosts.

### indieauth.yml
Creates and installs within the indieauth user a Ruby v2 environment and the IndieAuth code.

    ansible-playbook -i hosts indieauth.yml --limit indieauth-nyc

### ssh config
Adding the following to your ```~/.ssh/config``` will make things cleaner/easier to use ansible:

    Host indieauth-*
    StrictHostKeyChecking no
    UserKnownHostsFile=/dev/null

Just adjust the IdentityFile entry to point to your ssh key and then add the host names and IPs to your /etc/hosts file - you will be able to ssh in from the terminal *and* run ansible-playbooks against the same host names.
