Lustre
======

This role installs and configures Lustre clients.

Role Variables
--------------

By default, the role will install the Lustre modules built for the running
kernel. (`lustre-client-modules-{{ ansible_facts['kernel'] }}`). If one needs
to install DKMS packages instead, define `lustre_dkms: true`.

Example Playbook
----------------

Install and configure InfiniBand:

    - hosts: lustre_clients
      roles:
        - role: btravouillon.lustre
          tags: role::lustre
