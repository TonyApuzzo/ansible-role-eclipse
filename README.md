Ansible role eclipse
====================

[![Build Status](https://travis-ci.org/nwoetzel/ansible-role-eclipse.svg?branch=master)](https://travis-ci.org/nwoetzel/ansible-role-eclipse)

This Ansible Eclipse role is based on the work from [Nils Wötzel](https://github.com/nwoetzel) which one may find [here](https://github.com/nwoetzel/ansible-role-eclipse).
It has been modified to support more plugins and to remove the dependency [nwoetzel/ansible-role-oracle-java](https://github.com/nwoetzel/ansible-role-oracle-java) since I use ansiblebit.oracle-java to install Oracle Java.  See the bottom of this README for a code snippet to install and configure Java.

## Description

This ansible roles installs a eclipse distribution and optional plugins.

## Dependencies

- ansible >= 2
- Java JDK in PATH and/or JAVA_HOME environment variable set

## Role Variables

All defaults are documented also in the [defaults](defaults/main.yml) file.

| variable | required | default | description |
|--:|:-:|:-:|:--|
| eclipse_distro | yes | - | the eclipse distribution, e.g. mars, neon |
| eclipse_package | yes | - | the package (i.e. which default plugins are installed), e.g. java, php, cpp ... |
| eclipse_plugins_custom | no | {} | a dictionary of plugin declarations (to add more to the defaults in vars/main.yml or to overwrite) - read more in defaults/main.yml |
| eclipse_plugins_install | no | [] | list of plugin names to be installed, as they are defined in the [vars](vars/main.yml) or with the variable eclipse_plugins_custom |
| package_list_eclipse | no | [] | additional apt package names that should be installed |
| eclipse_download_folder_remote | no | - | when set, the file is downloaded to the installation host |
| eclipse_service_release | no | set by this [role](vars/main.yml) | the latest known is used when not set - depending on the distro can be 'SR2' (<=luna) or just '2' (>=mars) |
| eclipse_ini_overwrite | no | false | modify eclipse.ini |
| eclipse_ini_flags_next_line | no | {} | a dictionary of ini flags for eclipse, e.g. '"-vm": /opt/bin/java' |
| eclipse_ini_flags_vmargs | no | {} | a dictionary of vmargs for the java virtual machine, e.g. '"-XX:MaxPermSize=": "1024m"' |

## Facts

| variable | description |
|--:|:--|
| eclipse_install_dir | the directory eclipse is installed to |

## Possibilities

It is possible to install almost any [eclipse package of any distribution](http://www.eclipse.org/downloads/packages/). Currently, incubation packages and classic distributions are not supported.
Additinally, many plugins are preconfigured and can just be installed through their name.

## Limitations

Eclipse packages with incubation components or classic distributions are not supported yet.
Plugins need to be configured in the role, before they can be installed trough their name or repository url.
Adaption for either limitation can be easily implemented.

## Dependencies

```YAML
---
# Java Playbook snippet

- hosts: all
  gather_facts: yes
  roles:
    # Install Oracle Java
    - role: ansiblebit.oracle-java
  vars:
    java_shell_profile: no
  tasks:
    - name: Set Oracle Java as default
      # Copy /etc/profile.d/oracle_jdk.sh with content
      become: yes
      copy:
        content: |
                 #!/bin/bash
                 export JDK_HOME={{ oracle_java_home }}
                 export JAVA_HOME={{ oracle_java_home }}
                 export JRE_HOME={{ oracle_java_home }}/jre
                 export PATH=$PATH:{{ oracle_java_home }}/bin:{{ oracle_java_home }}/jre/bin
        dest: /etc/profile.d/oracle_jdk.sh
    # Export the env on th fly to make system wide change
    - name: Source the file /etc/profile.d/oracle_jdk.sh
      action: shell source /etc/profile
```

## License

[GPLv3](https://tldrlegal.com/license/gnu-general-public-license-v3-%28gpl-3%29)
