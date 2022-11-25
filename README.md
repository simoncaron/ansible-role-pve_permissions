Ansible Role: Proxmox VE Permissions (Users/ACLs/Groups/Roles/Pools)
=========

![Lint](https://github.com/simoncaron/ansible-role-pve_permissions/actions/workflows/lint.yml/badge.svg)

An Ansible Role that configures Users/Roles/Pools/Groups on a Proxmox VE node.

This role was tested on Proxmox VE 7.2.

Requirements
------------

None.

Role Variables
--------------

Available variables are listed below, along with default values (see `defaults/main.yml`):

    pve_permissions_roles: []

    pve_permissions_users: []

    pve_permissions_groups: []

    pve_permissions_pools: []

The `pve_permissions_roles` key should contain a list of roles to create. Each role should have two keys defined, `name` which set the role name and `privs` which is a list that should contain the privileges to bind with the role. A complete list of the privileges can be found [here](https://pve.proxmox.com/pve-docs/pveum.1.html) under Permission Management > Privileges. Example of role configuration:

    pve_permissions_roles:
      - name: TerraformProv
        privs:
          - VM.Allocate
          - VM.Clone
          - VM.Config.CDROM
          - VM.Config.CPU
          - VM.Config.Cloudinit
          - VM.Config.Disk
          - VM.Config.HWType
          - VM.Config.Memory
          - VM.Config.Network
          - VM.Config.Options
          - VM.Monitor
          - VM.Audit
          - VM.PowerMgmt
          - Datastore.AllocateSpace
          - Datastore.Audit

The `pve_permissions_users` key allows to define a list of users and the acls bound to a user on a Proxmox host. Each entry should contain a `name` and `realm` value. For new users (other than the default root), a `password` should also be provided. Other optional keys for each entry are `email`, `comment`, `enable` (1 or 0), `expire`, `groups`, `firstname` and `lastname`. Details on the value of each key can be found in [the PVE documentation](https://pve.proxmox.com/pve-docs/pveum.1.html). 

Each user should also contain a list of ACL permissions which must be granted to it un the the `acls` key. Each ACL defines a `path` and a `role`. Here's an example of a user configuration:

    pve_permissions_users:
      - name: terraform
        realm: pve
        email: test@email.com
        firstname: terra
        lastname: form
        comment: Terraform user
        enable: "1"
        expire: "0"
        groups:
          - Group1
          - Group2
        password: "password"
        acls:
          - path: /
            role: TerraformProv

NOTE: Due to a limitation of the `pveum` tool, it is not currently possible to assign new groups to an existing user.

The `pve_permissions_groups` key should contain a list of groups to create on the Proxmox instance. Each group entry should contain a `name` entry defining the group name and can also contain a `comment` value which will describe the group purpose. Here's an example of the groups configuration:

    pve_permissions_groups:
      - name: Group1
        comment: My Group

The `pve_permissions_pools` key should contain a list of pools to create on the Proxmox host. Each pool should have a `name` key set with the desired pool name and optionally a `comment` key describing the pool purpose. Here's an example of the pools configurations:

    pve_permissions_pools:
      - name: terraform
        comment: Resources managed using terraform
      - name: ansible
        comment: Resources managed using ansible
      - name: manual
        comment: Manually created resources

Dependencies
------------

None.

Example Playbook
----------------

    - hosts: localhost

      vars:
        pve_permissions_roles:
          - name: TerraformProv
            privs:
              - VM.Allocate
              - VM.Clone
              - VM.Config.CDROM
              - VM.Config.CPU
              - VM.Config.Cloudinit
              - VM.Config.Disk
              - VM.Config.HWType
              - VM.Config.Memory
              - VM.Config.Network
              - VM.Config.Options
              - VM.Monitor
              - VM.Audit
              - VM.PowerMgmt
              - Datastore.AllocateSpace
              - Datastore.Audit
        pve_permissions_pools:
          - name: terraform
            comment: terraform resources
        pve_permissions_users:
          - name: terraform
            realm: pve
            password: "password"
            acls:
              - path: /
                role: TerraformProv
      roles:
        - simoncaron.pve_permissions

License
-------

MIT

Author Information
------------------

This role was created in 2022 by [Simon Caron](https://simoncaron.com/).
