OpenSSL/OpenSSH Upgrade Ansible Playbook
==========================================

This is a guide to upgrade OpenSSL and OpenSSH to the latest
using ansible playbook.

Assumptions
-------------

* Ansible user in every node has a sudo privilege with NOPASSWD option.
* Ansible user in deployer host can connect to all hosts without 
  password prompt. (ssh-key-based login is assumed.)
* It supports only CentOS 7.

Prepare
--------

Copy default inventory to your site inventory.::

   $ cp -a inventory/default inventory/<your_site>

Edit <your site> inventory for your environment.::

   $ vi inventory/<your site>/hosts
   node1 ansible_connection=local
   node2
   node3
   
   [deployer]
   node1

Modify hostname, ip, etc...

Put hostname where you run this playbook in deployer group.

Copy ansible.cfg.sample to ansible.cfg.::

   $ cp ansible.cfg.sample ansible.cfg

Change inventory in ansible.cfg.::

   $ vi ansible.cfg
   inventory = inventory/<your site>/hosts
   ...

Change variable values.::

   $ vi inventory/<your site>/group_vars/all/vars.yml
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

If you have an internet connection on deployer, 
Use URL-based openssl_src and openssh_src.

Or else, get openssl and openssh source tarball and put them in 
the specified path on deployer.

Change openssl_version and openssh_version to the latest one.

Run
----

Now run the playbook.::

   $ ansible-playbook site.yml

If everything goes well, you will see something like this at the end.::

   TASK [openssh : Setup | Check openssh version is matched] **********************
   ok: [node1] => {
       "msg": "desired openssh version: 8.6p1 current openssh version: OpenSSH_8.6p1, OpenSSL 1.1.1k  25 Mar 2021"
   }
   ok: [node2] => {
       "msg": "desired openssh version: 8.6p1 current openssh version: OpenSSH_8.6p1, OpenSSL 1.1.1k  25 Mar 2021"
   }
   ok: [node] => {
       "msg": "desired openssh version: 8.6p1 current openssh version: OpenSSH_8.6p1, OpenSSL 1.1.1k  25 Mar 2021"
   }


Caveat)
After upgrading openssh, you may need to remove old host keys 
in $HOME/.ssh/known_hosts on deployer host since host keys are newly generated.

