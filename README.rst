Sysharden Ansible Playbook
===========================

Sysharden ansible playbook installs the latest OpenSSL, OpenSSH, and Kernel.

Assumptions
-------------

* Ansible user in every node has a sudo privilege with NOPASSWD option.
* Ansible user in deployer host can connect to all hosts without 
  password prompt. (ssh-key-based login is assumed.)
* It supports only CentOS 7 at the moment.

Prepare
--------

Copy default inventory to your site inventory.::

   $ cp -a inventory/default inventory/<your_site>

Edit <your site> inventory for your environment.::

   $ vi inventory/<your site>/hosts
   node1    ansible_host=<ip_address> ansible_connection=local
   node2    ansible_host=<ip_address>
   node3    ansible_host=<ip_address>
   
   [deployer]
   node1

Modify hostname and ip address (Use management ip address).
The hostname in deployer group is the host where you run this playbook.

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

   ## kernel
   # absolute file path for openssh source tarball (no internet environment)
   kernel_pkg: "/tmp/kernel-3.10.0-1160.25.1.el7.x86_64.rpm"
   # URL for openssl source tarball (if deployer has internet conneciton)
   #kernel_pkg: http://ftp.kaist.ac.kr/CentOS/7.9.2009/updates/x86_64/Packages/kernel-3.10.0-1160.25.1.el7.x86_64.rpm

Change openssl_version and openssh_version to the latest.

If you have an internet connection on deployer, 
Use URL-based openssl_src and openssh_src.

Or else, get source tarball and kernel rpm package and put them in 
the specified paths on deployer.


Check if every node is reachable.::

   $ ansible -m ping all
   node1 | SUCCESS => {
       "changed": false, 
       "ping": "pong"
   }
   node2 | SUCCESS => {
       "changed": false, 
       "ping": "pong"
   }
   node3 | SUCCESS => {
       "changed": false, 
       "ping": "pong"
   }


Run
----

Now run the playbook.::

   $ ansible-playbook site.yml

If everything goes well, you will see the upgrade info
at the end of each role.::

   ...
   TASK [iorchard.openssl : Check openssl version is matched] *********************
   Thursday 20 May 2021  18:40:23 +0900 (0:00:00.345)       0:10:13.932 **********
   ok: [node-1] => {
       "msg": "openssl version: 1.1.1k openssl version from command: 1.1.1k"
   }
   ok: [node-2] => {
       "msg": "openssl version: 1.1.1k openssl version from command: 1.1.1k"
   }
   ok: [node-0] => {
       "msg": "openssl version: 1.1.1k openssl version from command: 1.1.1k"
   }
   ...
   TASK [iorchard.openssh : Setup | Check openssh version is matched] *************
   Thursday 20 May 2021  18:42:59 +0900 (0:00:00.342)       0:12:49.398 ********** 
   ok: [node-1] => {
       "msg": "desired openssh version: 8.6p1 current openssh version: OpenSSH_8.6p
   1, OpenSSL 1.1.1k  25 Mar 2021"
   }
   ok: [node-2] => {
       "msg": "desired openssh version: 8.6p1 current openssh version: OpenSSH_8.6p
   1, OpenSSL 1.1.1k  25 Mar 2021"
   }
   ok: [node-0] => {
       "msg": "desired openssh version: 8.6p1 current openssh version: OpenSSH_8.6p
   1, OpenSSL 1.1.1k  25 Mar 2021"
   }
   ...
   TASK [iorchard.kernel : Install | List the installed kernels (version-release)] 
   ***
   Thursday 20 May 2021  18:45:48 +0900 (0:00:11.433)       0:15:38.605 ********** 
   ok: [node-1] => {
       "msg": "installed kernel: 3.10.0-1127.el7 3.10.0-1160.25.1.el7 "
   }
   ok: [node-0] => {
       "msg": "installed kernel: 3.10.0-1127.el7 3.10.0-1160.25.1.el7 "
   }
   ok: [node-2] => {
       "msg": "installed kernel: 3.10.0-1127.el7 3.10.0-1160.25.1.el7 "
   }



Caveat
--------

* After installing a new openssh, you may need to remove old host keys in 
  $HOME/.ssh/known_hosts on deployer host since host keys are newly generated.

* After installing a new kernel, you need to reboot each machine 
  to boot in the new kernel.

