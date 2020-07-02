Ansible Role: autofs
=========
[![Build Status](https://travis-ci.org/github/cmprescott/ansible-role-autofs.svg?branch=master)](https://travis-ci.org/github/cmprescott/ansible-role-autofs)

Installs and configures autofs.

Requirements
------------

```shell
# Ansible version 1.4.4+
ansible --version

# OS
case $OSTYPE in
  # Linux needs apt|yum|dnf|zypper
  "linux"*)
      apt --version||yum --version||dnf --version||zypper --version;;
  # OS X needs nothing
  "darwin"*)
      echo autofs is OOB;;
esac
```

Role Variables
--------------

```yaml
# --- autofs config ---
autofs_indirect_maps:
  - name: autofs.nfs
    path: /mnt/nfs
    mark: "unique identifier for idempotency"
    options: "--timeout=30 --ghost"
    mounts:
      - name: "isos"
        fstype: "nfs,rw,bg,hard,intr,tcp,resvport"
        url: "nfs.server.com:/data/isos"

# --- OS setup ---
# Shouldn't need to modify
autofs_pkgs:
  Linux: [ 'autofs' ]
  Darwin: []

# --- OS config ---
# Shouldn't need to modify
autofs_master:
  Linux: "/etc/auto.master"
  Darwin: "/etc/auto_master"
```

Dependencies
------------

None.

Example Playbook
----------------

```yaml
- name: "Media Client"
  hosts: clients.media
  roles:
    - name: "Media Client | NFS | ensure automounts"
      become: true
      become_method: sudo
      role: benyanke.autofs_ng
      autofs_indirect_maps:

        - name: "auto.nfs-nas"
          path: "/mnt/nfs"
          mark: "nfs mounts from NAS"
          options: "--timeout=30 --ghost"
          mounts:
            - name: "movies"
              fstype: "nfs,rw,bg,hard,intr,tcp,resvport"
              url: "nfs.server.com:/data/movies"
            - name: "tv"
              fstype: "nfs,rw,bg,hard,intr,tcp,resvport"
              url: "nfs.server.com:/data/tv"

        - name: "auto.nfs-other"
          path: "/-"
          mark: "NFS mount with root base"
          options: "--timeout=30 --ghost"
          mounts:
            - name: "/var/remotedir"
              fstype: "nfs,rw,bg,hard,intr,tcp,resvport"
              url: "nfs.server.com:/data/remotedir"
```

License
-------

BSD

Author Information
------------------

Prescott Chris
