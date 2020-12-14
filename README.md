# automatically setup UCD-Server and Agent on Linux (CENTOS/RHEL at the moment...)

## Why, When, What, Who, Where, How

I wanted to learn ANSIBLE. And i learn best when i do something practical. So the decision was to automate the setup of a demo environment for UrbanCode Deploy.

Started beginning of November 2019 with reading (Jeff Geerling: Ansible for DevOps, Lorin Hochstein & Rene Moser: Ansible Up and Running 2nd Ed) and hacking.

A lot of the used code has been found during my search on Ansible documentation, Ansible Galaxy, Stackoverflow, and so on. Didn't record the source to mention all of them here! My mistake...

Which environment i used:
Visual Studo Code with Ansible, Better Jinja, Jinja, Todo Tree Plugins
Ansible (Mac OS X) V 2.9.2 and (Windows 10 with WSL2 Ubuntu 20.04) V2.9.6
Python 3.7.5 on WSL2 it is 3.8.5
VMWare Fusion, VMWare Workstation Pro 16.1
CENTOS 7 V1908 freshly installed, nothing special set up!
CENTOS 8
UBUNTU 18.04 and 20.04

What is happening?
The freshly setup server is bootstraped so that Ansible can work (using Ansible to bootstrap it ;))
Server is updated, EPEL Repository is set for CENTOS/RHEL
Java, Docker (optional) and MySQL/MariaDB is installed
UCD database and user is created
UCD Server and Agent is installed and Agent is set as artifact import agent

Who am I:

~~~
Ing. Osman Burucu
DevOps Enthusiast
email: osman.burucu@at.ibm.com
IBM Austria, Vienna
~~~

## Steps

* create a local directory for all artifacts
  * example:
    * ~/Source/demo_assets as base directory for all
    * ~/Source/demo_assets/UCD with binaries of installation files
    * ~/Source/demo_assets/repos
* clone repos into repos directory

  ~~~sh
  cd repos
  git clone https://github.com/osmanburucu-ibm/deploy-urbancode-deploy.git
  ~~~
  
* install requirements for modules

  ~~~sh
  cd deploy-urbancode-deploy
  ansible-galaxy install -r requirements.yml
  ~~~

* edit the hosts file and set the target servername and ip address
* Set your Variables if the provided defaults do not fit
  * group_vars/all.yml
    * ***installfiles_location*** is set with default to ***~/Source/demo_assets/UCD***
    * ***download_dir*** is the directory on the target server where the artifacts will be downloaded. It is set to ***/tmp/downloads***
    * ***base_docker_install*** if set to ***yes*** Docker will be installed
    * ***base_java_xxx*** is used to install java with given versions.
  * group_vars/ucd_server.yml
    * ***mysql_connector_xxx*** which mysql connector to use
    * ***ucd installation files related variables*** 
      * ***ucd_version*** which UCD version will be installed. You can get the latest versions from your PA site or from IBM Fixcentral
    * ***install.properties related***
      * set the variables for the silent installation of UCD like, target installation directory, admin user/pwd, ucd server hostname, which ports to use and so on.
    * ***agent installation files related variables***
      * ***ucd_agent_name***: set the name of the agent, default is ucd.local
  * group_vars/db_server.yml
    * set the database related variables
* Start the setup with

  ~~~sh
  ansible-playbook setup-all-ucdsa.yml -u <ansible user for installation>
  ~~~

  * this will use the hosts file (see ansible.cfg)
  * after a few minutes your UrbanCode server will be finished

* installing other agents on different machines:

~~~sh
ansible-playbook -i hosts -u root setup-ucd-agents.yml -e 'ucd_server_hostname=<name of the ucd server>'
~~~

## Upgrading

Added a new playbook for upgrading the server in silent mode. Is is based on the informtation found here: [UrbanCode Deploy KnowledgeCenter](https://www.ibm.com/support/knowledgecenter/SS4GSP_7.1.0/com.ibm.udeploy.install.doc/topics/upgradeInstall.html)