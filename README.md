# Unarchive different remote files in different target servers

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT) [![GitHub release](https://img.shields.io/github/release/codeyourinfra/unarchive_from_url_param.svg)](https://github.com/codeyourinfra/unarchive_from_url_param/releases/latest) [![Build status](https://travis-ci.org/codeyourinfra/unarchive_from_url_param.svg?branch=master)](https://travis-ci.org/codeyourinfra/unarchive_from_url_param) [![Ansible Role](https://img.shields.io/ansible/role/42056.svg)](https://galaxy.ansible.com/codeyourinfra/unarchive_from_url_param) [![Ansible Role downloads](https://img.shields.io/ansible/role/d/42056.svg)](https://galaxy.ansible.com/codeyourinfra/unarchive_from_url_param)

This solution is explained in detail in the Codeyourinfra project blog post [How to unarchive different files in different servers in just one shot](http://codeyourinfra.today/how-to-unarchive-different-files-in-different-servers-in-just-one-shot). Check it out!

## Problem

You have a bunch of servers and quite often you have to manually extract in each one a specific compressed file. It works well when there's not too many servers to change, but such task becomes really boring and even error-prone when it reaches, say, a hundred servers or more. How can it be done in scale?

## Solution

**unarchive_from_url_param** is an example of [Ansible role](https://docs.ansible.com/ansible/latest/user_guide/playbooks_reuse_roles.html) which makes it possible. You just need to pass the parameters appropriately. For each server, the role expects a **url** from where the compressed file is downloaded and a **target** directory to where the content is extracted. All the hosts that are defined inside the **servers** group in the [Ansible inventory](https://docs.ansible.com/ansible/latest/user_guide/intro_inventory.html) are considered on a single execution.

```yml
---
- hosts: servers
  roles:
    - role: unarchive_from_url_param
      vars:
        params:
          server1:
            url: https://archive.apache.org/dist/maven/maven-3/3.5.0/binaries/apache-maven-3.5.0-bin.tar.gz
            target: /opt/maven
          server2:
            url: https://archive.apache.org/dist/ant/binaries/apache-ant-1.10.1-bin.zip
            target: /opt/ant
```

## Test

First of all, turn on the VMs, executing the command `$ vagrant up`. Once the test environment is up, execute the command `$ ansible-playbook playbook.yml`. Finally, in order to check if the compressed files were properly extracted in both servers, execute the command `$ ansible servers -m shell -a "ls /opt"`.

### Automated tests

You can also test the solution automaticaly, by executing `./test.sh` or using [Molecule](https://molecule.readthedocs.io). If you prefer the latter, use [venv](https://docs.python.org/3/tutorial/venv.html) to setup the test environment:

`python3 -m venv env && source env/bin/activate && pip install -r requirements.txt`

After that, you can choose to perform the test through 3 different ways:

#### 1. Using local VMs (the default)

`molecule test`

As well as the shell script [test.sh](test.sh), it requires:

- [VirtualBox](https://www.virtualbox.org)
- [Vagrant](https://www.vagrantup.com)

#### 2. Using EC2 instances

`molecule test -s aws`

If your choice is performing the test in AWS, bear in mind you must have your credentials appropriately in **~/.aws/credentials**. You can [configure it through the AWS CLI tool](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html).

The test is performed in the AWS region *Europe - London (eu-west-2)*. Just run the test and check the running instances through your [AWS Console](https://eu-west-2.console.aws.amazon.com/ec2/v2).

#### 3. Using Docker containers

`molecule test -s docker`

That's the way through [the role is tested in Travis CI](https://travis-ci.org/codeyourinfra/unarchive_from_url_param).

It requires [Docker](https://www.docker.com).
