Lustre
======

This role installs and configures Lustre clients.

Role Variables
--------------

TBC

Example Playbook
----------------

Install and configure InfiniBand:

    - hosts: lustre_clients
      roles:
        - role: btravouillon.lustre
          tags: role::lustre
