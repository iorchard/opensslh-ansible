iorchard.openssl
=================

Ansible role to install the latest openssl from source

Requirements
------------

This role requires Ansible 2.6 or higher.

This role supports:

  - CentOS 7

Role Variables
--------------

[defaults/main.yml](defaults/main.yml)

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables
passed in as parameters) is always nice for users too:

    hosts: servers
    roles:
      - {role: iorchard.openssl, tags: openssl}

License
-------

  - Code released under [Apache License 2.0](LICENSE)
  - Docs released under [CC BY 4.0](http://creativecommons.org/licenses/by/4.0/)

Author Information
------------------

  - Heechul Kim @iOrchard
      - <https://github.com/iorchard>

