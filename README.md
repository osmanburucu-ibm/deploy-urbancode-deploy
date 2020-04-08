# automatically setup UCD-Server and Agent on Linux (CENTOS/RHEL at the moment...)

## Why, When, What, Who, Where, How

I wanted to learn ANSIBLE. And i learn best when i do something practical. So the decision was to automate the setup of a demo environment for UrbanCode Deploy.

Started beginning of November 2019 with reading (Jeff Geerling: Ansible for DevOps, Lorin Hochstein & Rene Moser: Ansible Up and Running 2nd Ed) and hacking.

A lot of the used code has been found during my search on Ansible documentation, Ansible Galaxy, Stackoverflow, and so on. Didn't record the source to mentioned all of them here! My mistake...

Which environment i used:
Visual Studo Code with Ansible, Better Jinja, Jinja, Todo Tree Plugins
Ansible (Mac OS X) V 2.9.2
Python 3.7.5
VMWare Fusion
CENTOS 7 V1908 freshly installed, nothing special set up!
RHEL 7.7
UBUNTU 18.04

What is happening?
The freshly setup server is bootstraped so that Ansible can work (using Ansible to bootstrap it ;))
Server is updated, EPEL Repository is set for CENTOS/RHEL
Java, Docker and MySQL/MariaDB is installed
UCD database and user is created
UCD Server and Agent is installed and Agent is set as artifact import agent

Who am I:
Ing. Osman Burucu
DevOps Enthusiast
email: osman.burucu@at.ibm.com
IBM Austria, Vienna

## Steps

1. Put all installation material into one directory locally (which will be used for the installfiles_location variable!)
    1. [***TODO***](#1.1) Need a more flexible way to access installation files (f.e. artifactory, web url, ftp site, ....)
2. Install all playbook requirements
    1. ansible-galaxy install -r requirements.yml
3. Then set hosts
    1. [***TODO***](#3.1): at the moment ucd_server and db_server has to be the same as connection to mariadb works only over localhost
    2. [***TODO***](#3.2): BluePrint Engine/Designer etc. are not used at the moment
    3. set the FQDN and the IP address of the servers
4. Set your Variables
    1. [group_vars/all.yml](group_vars/all.yml)
        1. installfiles_location = the LOCAL directory where all the installation files (zipped) are to be found
        2. download_dir = the REMOTE location where the "downladed" files to be stored
    2. [group_vars/ucd_server.yml](group_vars/ucd_server.yml)
        1. mysql/mariadb variables
            1. only the provided mysql connector worked
            2. [mysql-connector](https://dev.mysql.com/get/Downloads/Connector-J/mysql-connector-java-8.0.19.zip)
        2. UCD Server variables
            1. need the file name of UCD server zip file (got this one from fix-central! ibm-ucd-7.0.4.1.1036185.zip)
            2. ucds_install_dir_name: is the name of the directory after unzipping the provided file from above
            3. ucds_install_log_name: will be created during installation and will show stdout and stderr!
                1. installation will skip when this file exists!
        3. UCD Server install.properties variables
            1. are used to populate the installation.properties file for silent install!
            2. set target directory (standard is /opt/ucd/server)
            3. set the FQDN of the server name (--> see hosts file as this will be used! and not the actual hostname from server!)
            4. which user/group is used to run the UCD-server process (at the moment root!!)
                1. [***TODO***](#4.2.3.4.1): change UCD process user/group to different user instead of root
            5. ucds_admin_user and ucds_initial_admin_pwd: initial UCD Server admin user and password
            6. install_server_web_https_port: which port to use for HTTPS (default is 8443)
            7. agentcomm_uri_port: which port to use for UCD Server - Agent communication (default is 7919)
        4. UCD Agent variables
            1. ucda_file_name: name of the zip file which will be downloaded from UCD Server
            2. ucda_install_log_name: log file which will be used during silent installation.
                1. installation will skip when this file exists!
            3. ucd_agent_name: uniqe agent name (use again inventory_hostname with -ucdagent as addon)
        5. UCD Agent agent.install.properties variables
            1. ucda_install_dir_name: name of the directory after unzipping the downloaded ucd-agent.zip from server
            2. install_agent_dir: set target directory (standard is /opt/ucd/agent)
    3. [group_vars/db_server](group_vars/db_server)
        1. mariadb/mysql variables
            1. define the mysql/mariadb service and port to use (default are mysqld and 3306)
            2. set the hostname of the mysql/mariadb server (default localhost -> [***TODO***](#4.3.1.2): due some remote access problems it has to be localhost...)
        2. UCD Server Database variables
            1. database name (default: ibm_ucd)
            2. database user and password (default: ibm_ucd/ibm_ucd)
5. Setup SSH connection [***TODO***](#5) Need to have a more flexible, stable and easier solution for that (also need different users!)
    1. Need to have key exchange with hosts so that during ansible playbook execution no password is queried
    2. run [script](helper_scripts/addtoknownhosts.sh) to accept remote host keys to known hosts and copy local users key to remote host
        1. password from remote host (user: root) will be asked here
6. Run setup-all-ucdsa.yml playbook which will execute all other playbooks in the right order

    ~~~sh
    ansible-playbook setup-all-ucsa.yml -u root
    ~~~

    [***TODO***](#6) playbook has to be started with root user when local user is not setup as user on remote host (need sudo and sudo with no passwd settings)

---

## Todo's

### OPEN Todo's

***Todo's overall***

#### Security

TODO: on RHEL/CENTOS use SELINUX enabled (at the moment it has to be disabled)
TODO: provide ways to create users with pwd on remote host(s) - f.e. needed for non root execution of playbook...

#### provisioning of servers

TODO: need provisioning of resources
TODO: TerraForm for IBM Cloud
TODO: ?? TerraForm or other for VMWare
TODO: TerraForm or Vagrant for VirtualBox

#### UrbanCode BluePrint Designer

TODO: UCD BPD (Engine, Designer and so on) need also to be setup

#### Demo Artifacts

TODO: provide demo artefacts (f.e. JPetStore application) in UCD

***Todo's described in the steps section***

#### 1.1

TODO: Need a more flexible way to access installation files (f.e. artifactory, web url, ftp site, ....)

#### 3.1

TODO: at the moment ucd_server and db_server has to be the same as connection to mariadb works only over localhost

#### 3.2

TODO: BluePrint Engine/Designer etc. are not used at the moment

#### 4.2.3.4.1

TODO: change UCD process user/group to different user instead of root

#### 4.3.1.2

TODO: due some remote access problems it has to be localhost...

#### 5

TODO: Need to have a more flexible, stable and easier solution for that (also need different users!)

#### 6

TODO: playbook has to be started with root user when local user is not setup as user on remote host (need sudo and sudo with no passwd settings)
