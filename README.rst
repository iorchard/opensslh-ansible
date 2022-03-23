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
   node-0   ansible_host=<ip_address> ansible_connection=local
   node-1   ansible_host=<ip_address>
   node-2   ansible_host=<ip_address>
   
   [deployer]
   node-0

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
   node-0 | SUCCESS => {
       "changed": false, 
       "ping": "pong"
   }
   node-1 | SUCCESS => {
       "changed": false, 
       "ping": "pong"
   }
   node-2 | SUCCESS => {
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
   TASK [iorchard.openssl : Test | Check openssl version is matched] *****************************************
   Friday 21 May 2021  12:52:31 +0900 (0:00:00.364)       0:09:59.488 ************ 
   ok: [node-1] => {
       "msg": "Desired openssl version: 1.1.1k | Installed openssl version: 1.1.1k"
   }
   ok: [node-2] => {
       "msg": "Desired openssl version: 1.1.1k | Installed openssl version: 1.1.1k"
   }
   ok: [node-0] => {
       "msg": "Desired openssl version: 1.1.1k | Installed openssl version: 1.1.1k"
   }
   ...
   TASK [iorchard.openssh : Test | Check openssh version is matched] *****************************************
   Friday 21 May 2021  12:55:03 +0900 (0:00:00.370)       0:12:31.458 ************ 
   ok: [node-1] => {
       "msg": "Desired openssh version: 8.6p1 | Installed openssh version: OpenSSH_8.6p1, OpenSSL 1.1.1k  25 Mar 2021"
   }
   ok: [node-2] => {
       "msg": "Desired openssh version: 8.6p1 | Installed openssh version: OpenSSH_8.6p1, OpenSSL 1.1.1k  25 Mar 2021"
   }
   ok: [node-0] => {
       "msg": "Desired openssh version: 8.6p1 | Installed openssh version: OpenSSH_8.6p1, OpenSSL 1.1.1k  25 Mar 2021"
   }
   ...
   TASK [iorchard.kernel : Test | List the installed kernels (version-release)] ******************************
   Friday 21 May 2021  12:57:41 +0900 (0:00:08.464)       0:15:09.813 ************ 
   ok: [node-1] => {
       "msg": "Desired kernel: 3.10.0-1160.25.1 | Installed kernels: 3.10.0-1127.el7, 3.10.0-1160.25.1.el7, "
   }
   ok: [node-0] => {
       "msg": "Desired kernel: 3.10.0-1160.25.1 | Installed kernels: 3.10.0-1127.el7, 3.10.0-1160.25.1.el7, "
   }
   ok: [node-2] => {
       "msg": "Desired kernel: 3.10.0-1160.25.1 | Installed kernels: 3.10.0-1127.el7, 3.10.0-1160.25.1.el7, "
   }

If you want to run each role, use --tags.

To run openssl role.::

   $ ansible-playbook --tags openssl site.yml

To run openssh role.::

   $ ansible-playbook --tags openssh site.yml

Always run openssl role before openssh role since openssh role depends on
openssl role.

To run kernel role.::

   $ ansible-playbook --tags kernel site.yml

Installed Directories
-----------------------

* openssl: /opt/openssl-<version>
* openssh: /opt/openssh-<version>

Backup files
----------------

* openssl: /usr/bin/openssl-<version>
* openssh: /usr/bin/ssh-<version>, /usr/sbin/sshd-<version>

Caveat
--------

* After installing a new openssh, you may need to remove old host keys in 
  $HOME/.ssh/known_hosts on deployer host since host keys are newly generated.

* After installing a new kernel, you need to reboot each machine 
  to boot in the new kernel.

