Ansible role proxmox_create_lxc
=========

After seeing the proof of concept of the proxmox module, we did a more complete role for LXC container creation in a proxmox cluster. with network fulluy configured and eventually several disks with acl and quotas management. 

Requirements
------------

You must act on a proxmox node or cluster already configured. 

Role Variables
--------------

The defaults define the container parameters. To be specified by host under `host_vars/host_fqdn/vars` and eventually encrypted in `host_vars/host_fqdn/vault`

Dependencies
------------

none right now. 

A list of other roles hosted on Galaxy should go here, plus any details in regards to parameters that may need to be set for other roles, or variables that are used from other roles.

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

    - hosts: servers
      roles:
         - { role: username.rolename, x: 42 }

License
-------

GPLv3

Author Information
------------------

Ulvida daniel@uruguayos.fr
https://github.com/UdelaRInterior
https://proyectos.interior.edu.uy/i