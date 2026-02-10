# Lustre

This role installs and configures Lustre clients for high-performance computing
environments.

## Role Variables

### Required Variables

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

### Optional Variables

#### Repository Configuration
- `lustre_configure_repos`: Configure Lustre repositories (default: false)
  - When true, requires `lustre_apt_repository` to be set
- `lustre_apt_repository`: APT repository URL for Lustre packages
  - Example: 'deb https://downloads.whamcloud.com/public/lustre/latest-release/ubuntu2204/client/ ./'
- `lustre_gpg_key`: URL of the GPG key for the Lustre repository

#### Installation Configuration
- `lustre_upgrade`: Upgrade Lustre packages to latest version (default: false)

#### DKMS Configuration
- `lustre_dkms`: Use DKMS packages instead of pre-built modules (default: false)
  - When false (default): Installs pre-built modules for the running kernel
    (`lustre-client-modules-{{ ansible_facts['kernel'] }}`)
  - When true: Installs DKMS packages to build modules for installed kernels
- `lustre_dkms_patches`: List of patches to apply to DKMS modules
  ```yaml
  lustre_dkms_patches:
    - src: patches/0001-fix.patch
      version: "2.15.6"
      order: 0
    - src: patches/0002-fix.patch
      version: "2.15.6"
      order: 1
  ```
  Each patch must have:
  - `src`: Path to the patch file
  - `version`: Lustre version to apply the patch to
  - `order`: Order in which to apply the patch (must be sequential starting from 0)

#### LNet Configuration
- `lustre_lnet_conf`: Custom LNet configuration (see defaults/main.yml for example)
- `lustre_lnet_networks`: LNet network configuration (default: "o2ib0(ib0)")
- `lustre_lnet_restart`: Restart LNet service after configuration (default: false)
- `lustre_lnet_systemd_override`: Configure a drop-in file for LNet service

#### Lustre Configuration
The role uses a basic template to configure `lustre.conf` by default.

To override this template:
- `lustre_conf`: Custom lustre.conf configuration (see defaults/main.yml for example)

## Example Playbooks

### Basic Installation

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

### With DKMS Patches

```yaml
- hosts: lustre_clients
  roles:
    - role: btravouillon.lustre
      vars:
        lustre_configure_repos: true
        lustre_dkms: true
        lustre_dkms_patches:
          - src: patches/0001-fix.patch
            version: "2.15.6"
            order: 0
          - src: patches/0002-fix.patch
            version: "2.15.6"
            order: 1
        lustre_filesystems:
          - src: lustre@o2ib0:/lustre
            path: /mnt/lustre
            opts: rw,noatime
            boot: true
```

## Requirements

- Ansible 9 or higher
- Debian-based system (Ubuntu, Debian)
- Network connectivity to Lustre servers

## Troubleshooting

### DKMS Patches

1. **Patch Application Fails**
   - Verify patch file format is correct
   - Check if patch version matches installed Lustre version
   - Ensure patch order is sequential starting from 0

2. **Module Build Fails**
   - Check DKMS logs: `/var/lib/dkms/lustre-client-modules/<kernel-version>/log/make.log`
   - Verify kernel headers are installed
   - Check if patch conflicts with existing code

3. **Module Not Loading**
   - Check module status: `dkms status -m lustre-client-modules -v <version>`
   - Verify module is built for current kernel
   - Check system logs for errors

## License

MIT

## Author Information

- Bruno Travouillon [@btravouillon](https://github.com/btravouillon)

## Support

Report bugs and feature requests on [GitHub Issues](https://github.com/btravouillon/ansible-role-lustre/issues)
