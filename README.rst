OpenSSL/OpenSSH Upgrade Ansible Playbook
==========================================

This is a guide to upgrade OpenSSL and OpenSSH to the latest
using ansible playbook.

Assumptions
-------------

* The first host in inventory file is the ansible controller host.
* Ansible user in every node has a sudo privilege with NOPASSWD option.
* Ansible user in controller host can connect to all hosts without 
  password prompt. (ssh-key-based login is assumed.)
* It supports only CentOS 7.

Prepare
--------

Edit default inventory for your environment.::

   $ vi inventory/default/hosts

Modify hostname, ip, etc...

Put hostname where you run this playbook in deployer group.

Change variable values.::

   $ vi inventory/default/group_vars/all/vars.yml
   ## openssl
   openssl_version: "1.1.1k"
   
   # absolute file path for openssl source tarball (no internet environment)
   openssl_src: "/tmp/openssl-{{ openssl_version }}.tar.gz"
   # URL for openssl source tarball (when there is a connection to internet)
   #openssl_src: "https://www.openssl.org/source/openssl-{{ openssl_version }}.tar.gz"
   
   # openssh
   openssh_version: "8.6p1"
   
   # absolute file path for openssh source tarball (no internet environment)
   openssh_src: "/tmp/openssh-{{ openssh_version }}.tar.gz"
   # URL for openssl source tarball (when there is a connection to internet)
   #openssh_src: "https://ftp.jaist.ac.jp/pub/OpenBSD/OpenSSH/portable/openssh-{{ openssh_version }}.tar.gz"

If you have an internet connection on ansible controller, 
Use URL-based openssl_src and openssh_src.

Or else, get openssl and openssh source tarball and put them in 
the specified path on ansible controller host.

Change openssl_version and openssh_version to the latest one.

Run
----

Now you are ready to run the playbook.::

   $ ansible-playbook site.yml


