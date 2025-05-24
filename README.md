Lustre
======

This role installs and configures Lustre clients for high-performance computing
environments.

Role Variables
--------------

Required variables:
- `lustre_filesystems`: List of Lustre filesystems to mount
  ```yaml
  lustre_filesystems:
    - src: lustre@tcp0:/lustre    # Required: Lustre filesystem source
      path: /mnt/lustre           # Required: Mount point
      opts: rw,noatime            # Optional: Mount options (will be appended with
                                  # _netdev,x-systemd.automount,x-systemd.requires=lnet.service)
      boot: true                  # Optional: Mount at boot (default: false)
      state: mounted              # Optional: Mount state (default: mounted)
  ```

Optional variables:
- `lustre_configure_repos`: Configure package repositories (default: false)
- `lustre_apt_priority`: APT repository priority
- `lustre_apt_repository`: APT repository URL for Lustre packages. The URL determines
  which version will be installed:

  ```yaml
  # Latest stable release
  lustre_apt_repository: 'deb https://downloads.whamcloud.com/public/lustre/latest-release/ubuntu2204/client/ ./'

  # Specific version (example)
  lustre_apt_repository: 'deb https://downloads.whamcloud.com/public/lustre/lustre-2.15.1/ubuntu2204/client/ ./'
  ```
  Note: The repository URL must match your Ubuntu version (e.g. ubuntu2204)

- `lustre_gpg_key`: URL of the GPG key for the Lustre repository
- `lustre_dkms`: Use DKMS packages instead of pre-built modules (default: false)
  - When false (default): Installs pre-built modules for the running kernel
    (`lustre-client-modules-{{ ansible_facts['kernel'] }}`)
  - When true: Installs DKMS packages to build modules for installed kernels
- `lustre_lnet_conf`: Custom LNet configuration (see defaults/main.yml for example)
- `lustre_lnet_networks`: LNet network configuration (default: "o2ib0(ib0)")
- `lustre_lnet_restart`: Restart LNet service after configuration (default: false)
- `lustre_upgrade`: Upgrade Lustre packages to latest version (default: false)

Example Playbook
----------------

Basic installation:
```yaml
- hosts: lustre_clients
  roles:
    - role: btravouillon.lustre
      vars:
        lustre_configure_repos: true
        lustre_apt_repository: 'deb https://downloads.whamcloud.com/public/lustre/latest-release/ubuntu2204/client/ ./'
        lustre_filesystems:
          - src: lustre@tcp0:/lustre
            path: /mnt/lustre
```

Advanced configuration with InfiniBand:
```yaml
- hosts: lustre_clients
  roles:
    - role: btravouillon.lustre
      vars:
        lustre_configure_repos: true
        lustre_apt_repository: 'deb https://downloads.whamcloud.com/public/lustre/latest-release/ubuntu2204/client/ ./'
        lustre_lnet_networks: "o2ib0(ib0)"
        lustre_filesystems:
          - src: lustre@o2ib0:/lustre
            path: /mnt/lustre
            opts: rw,noatime
            boot: true
```

Requirements
------------

- Ansible 9 or higher
- Supported OS: Debian-based systems
- Network connectivity to Lustre servers

License
-------

MIT

Author Information
------------------

- Bruno Travouillon [@btravouillon](https://github.com/btravouillon)

Support
-------

Report bugs and feature requests on [GitHub Issues](https://github.com/btravouillon/ansible-role-lustre/issues)
