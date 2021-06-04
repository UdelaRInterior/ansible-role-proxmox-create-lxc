Ansible role proxmox_create_lxc
=========

[![Galaxy](https://img.shields.io/badge/galaxy-UdelaRInterior.proxmox__create__lxc-blue.svg)](https://galaxy.ansible.com/udelarinterior/proxmox_create_lxc) ![GitHub tag (latest by date)](https://img.shields.io/github/v/tag/udelarinterior/ansible-role-proxmox-create-lxc?label=release&logo=github&style=social) ![GitHub stars](https://img.shields.io/github/stars/udelarinterior/ansible-role-proxmox-create-lxc?style=social) ![GitHub forks](https://img.shields.io/github/forks/udelarinterior/ansible-role-proxmox-create-lxc?style=social)

A complete role for LXC container creation in a Proxmox Virtual Environement (PVE) cluster, with network fully configured and eventually several disks with acl and quotas management. The role wraps the [community `proxmox` collection](https://docs.ansible.com/ansible/latest/collections/community/general/proxmox_module.html#parameter-description)

Requirements
------------

You must act on a Proxmox node or cluster already configured, i.e. you need Proxmox Virtual Environement (PVE) node already installed (tested with PVE 5 and 6), and a Proxmox user with LXC container creation rights.

Yo also need an SSH key configured in the local machine, where ansible is ran, i.e. a file `~/.ssh/id_rsa.pub`.


Installation
------------

```shell
$ ansible-galaxy install udelarinterior.proxmox_create_lxc
```

To be able to update later and eventually to modify it, prefer using `requirements.yml` with the git source:

```yaml
- name: udelarinterior.proxmox_create_lxc
  src: https://github.com/UdelaRInterior/ansible-role-proxmox-create-lxc.git
```
And then download it with `ansible-galaxy`:

```shell
$ ansible-galaxy install -r requirements.yml
```

Using `git`, you'll have to be carefull to folder name:

```shell
$ git clone https://github.com/UdelaRInterior/ansible-role-proxmox-create-lxc.git udelarinterior.proxmox_create_lxc
```

Role Variables
--------------

The `defaults` variables define the container parameters. To be specified by host under `host_vars/host_fqdn/vars` and eventually encrypted in `host_vars/host_fqdn/vault`.

New interface introduced in v3.0.0 is maintained, with role's variables defined in the `pve_*` namespace when they are shared between several Proxmox roles, and in the `pve_lxc_*` namespace when they are specific to the present one. The role is no longer backward's compatible with [v2.X.Y previous interface](https://github.com/UdelaRInterior/ansible-role-proxmox-create-lxc/blob/v2.2.0/README.md#role-variables) and the role gives no longer a default value to `pve_lxc_root_password` in order to avoid unsecure container creation.

```yaml
#########################################################
### Proxmox API connection and authentication section ###
#########################################################

# Various module options used to have default values. This cause problems when user expects different behavior from proxmox
# by default or fill options which cause problems when they have been set. The default value is compatibility, which will
# ensure that the default values are used when the values are not explicitly specified by the user.From community.general
# 4.0.0 on, the default value will switch to no_defaults. To avoid deprecation warnings, please set proxmox_default_behavior
# to an explicit value. This affects the disk, cores, cpus, memory, onboot, swap, cpuunits options.
# See https://docs.ansible.com/ansible/latest/collections/community/general/proxmox_module.html#parameter-proxmox_default_behavior
pve_lxc_proxmox_default_behavior: compatibility

# Proxmox node hostname where we create or manage an LXC container
pve_node: mynode

# FQDN or IP of the Proxmox API endpoint where we manage the cluster or node
pve_api_host: mynode.mycluster.org

# User to use to connect to the Proxmox cluster API
pve_api_user: automations@pam    # Optional if token based authentication is used
# Password for the previous API user (BETTER PUT THIS IN A VAULT, this dummy example can cause security issues)
pve_api_password: PaSsWd_f0r-AuToMaTi0nS    # Optional if token authentication is used

# pve_api_token_id: automations@pam!ansible                       # Optional if user-password based authentication is used
# pve_api_token_secret: 0b029a23-1ca3-a93f-8d90-5a4c9d064199      # Optional if user-password based authentication is used


#####################################
### Container OS template section ###
#####################################

# Proxmox LXC template we use to create the container
# The name of an appliance container template (pveam)
pve_lxc_ostemplate_name: debian-10.0-standard_10.0-1_amd64.tar.gz
    # Optional. It is used only if a name of an appliance container template (pveam) was not defined in the variable pve_lxc_ostemplate_name
# pve_lxc_ostemplate_url: http://download.proxmox.com/images/system/debian-10.0-standard_10.0-1_amd64.tar.gz

# pve_lxc_ostemplate_src: /mnt/os-images/ubuntu-20.04-standard_20.04-1_amd64.tar.gz   # Optional.
pve_lxc_ostemplate_storage: local                                                     # Optional.
pve_lxc_ostemplate_content_type: vztmpl                                               # Optional.
pve_lxc_ostemplate_timeout: 60 # in seconds                                           # Optional.
pve_lxc_ostemplate_force: no                                                          # Optional.
pve_lxc_ostemplate_validate_certs: no                                                 # Optional.
pve_lxc_ostemplate_state: present                                                     # Optional.


##############################################
### Container resources definition section ###
##############################################

# You can change the timeout for operations of the module according to the performance of your remote host
pve_lxc_timeout: 30                             # Optional.

# Validate the node's certificates when creating the container
pve_lxc_validate_certs: no                      # Optional.

# By default, we suppose that `inventory_hostname` is the FQDN or the hostname of the host to create, so we set the variable to the hostname.
# You can arbitrarly define this hostname
pve_hostname: "{{ inventory_hostname.split('.')[0] }}"

pve_lxc_vmid: 200                               # Optional.

pve_lxc_description: |                          # Optional.
  This host is a Debian Buster example container configured via Ansible with:
  - 1 CPU cores
  - 512MB of RAM
  - 16GB of system disk
  - unprivileged: yes
  - onboot: no

pve_lxc_root_password: please-use-a-custom-complex-string # no default value for security reasons, put yours in a vault.
pve_lxc_root_authorized_pubkey: "{{ lookup('file', '~/.ssh/id_rsa.pub') }}" # Ansible controller default public key

pve_lxc_cpu_cores: 1                              # Optional.
pve_lxc_cpu_limit: 1                              # Optional.
pve_lxc_cpu_units: 1000                           # Optional.

pve_lxc_memory: 512                               # Optional.
pve_lxc_swap: 512                                 # Optional.

# # Size (in GB) of the main disk we configure for the LXC
pve_lxc_disk: 16                                  # Optional.
pve_lxc_storage: local-lvm

# Start the container when node boot. It is recommended setting in 'yes' when container is in production
pve_lxc_onboot: no                                # Optional.

# pve_lxc_ip_address: 192.168.33.10               # Optional.
pve_lxc_nameserver: 1.1.1.1 1.0.0.1               # Optional.
pve_lxc_searchdomain: mycluster.org               # Optional.
pve_lxc_unprivileged: yes                         # Optional.
pve_lxc_force: no                                 # Optional.
pve_lxc_features:                                 # Optional.
  - nesting=1

pve_lxc_hookscript: 'local:snippets/vm_hook.sh' # Optional.

# List of network interfaces and their characteristics
pve_lxc_net_interfaces:
  - id: net0
    name: eth0
    hwaddr: F6:A2:69:61:94:8D   # Optional. If not indicated, Proxmox will assign one automatically.
    ip4: 192.168.33.10          # Optional. Available options: valid IPv4 address (to use static) - dhcp (to use DHCP)
    netmask4: 24                # Optional if IPv4 not indicated.
    gw4: 192.168.33.1           # Optional.
    ip6: 200:db8::10            # Optional. Available options: valid IPv6 address (to use static) - dhcp (to use DHCP) - auto (to use SLAAC)
    netmask6: 64                # Optional if IPv6 not indicated.
    gw6: 200:db8::1             # Optional.
    bridge: vmbr0
    firewall: false             # Optional. # Setting netif_firewall in TRUE, enable the use of firewall on the network interface
    rate_limit: 5               # Optional. (In MB/s)
    vlan_tag: 200               # Optional.
  - id: net1
    name: eth1
    ip6: 200:db8::10
    netmask6: 64
    bridge: vmbr1

# List of additional mount points and their characteristics
pve_lxc_mounts:
  - id: mp0
    storage: local-lvm
    size: 16
    mount_point: "/mnt/data"
    acl: false                     # Optional.
    quota: false                   # Optional.
    backup: false                  # Optional.
    skip_replication: false        # Optional.
    read_only: false               # Optional.
  - id: mp1
    storage: local-lvm
    size: 8
    mount_point: "/mnt/logs"

# Additional "manual" settings to add to the file /etc/pve/nodes/{{ node }}/lxc/{{ VMID }}.conf
pve_lxc_additional_configurations: []
    # Kernel modules available within the LXC
  - regexp: '^mp0'
    line: 'mp0: /lib/modules/4.15.18-9-pve,mp=/lib/modules/4.15.18-9-pve,ro=1'
    state: present
    # Enable/Disable additional features
  - regexp: '^features'
    line: 'features: nesting=1'
    state: present
    # tun device for OpenVPN server inside LXC
  - regexp: '^lxc.cgroup.devices.allow'
    line: 'lxc.cgroup.devices.allow = c 10:200 rwm'
    state: present
  - regexp: '^lxc.hook.autodev'
    line: 'lxc.hook.autodev = sh -c "modprobe tun; cd ${LXC_ROOTFS_MOUNT}/dev; mkdir net; mknod net/tun c 10 200; chmod 0666 net/tun"'
    state: present
```

Dependencies
------------

We need Ansible version > 2.10 and `community.general` collection to have the appropriate API of Proxmox modules.

Proxmox VE 5 or higher installed in a server node or a cluster of several nodes.

Example Playbook
----------------

Let's say, as the vars example, `mynode.mycluster.org` is our PVE node NS (api on port 8006), `mynode` it's pve node name, and `deploy` our Proxmox user in this node, and `pve_containers_group` an Ansible group of the containers to define.

Given that:
* containers are named `<container>.node.mycluster.org`,
* Name resolutions of PVE containers and node are configured,
* containers' variables are defined (for example in each `host_vars/<container>/vars/`),
* All new IPs are allocated and routed,

the following playbook creates all the containers declared in the `pve_containers_group`,

    - name: create containers declared in pve_containers_group
      hosts: pve_containers_group
      remote_user: deploy
      become: yes
      gather_facts: no

      roles:
        - udelarinterior.proxmox_create_lxc

will create and start the containers, and configure root access with the `pve_lxc_root_password` and the SSH key defined for it (your local machine `~/.ssh/id_rsa.pub` by default).

BE CAREFULL: contrairly to Debian standard installation, Proxmox container templates let remote root SSH open.

License
-------

(c) Universidad de la República (UdelaR), Red de Unidades Informáticas de la UdelaR en el Interior.

Licenced under GPL-v3

Author Information
------------------

[@ulvida](https://github.com/ulvida) (daniel@uruguayos.fr)
[@santiagomr](https://github.com/santiagomr)
[@UdelaRInterior](https://github.com/UdelaRInterior)
https://proyectos.interior.edu.uy/

Inspired in [this Proxmox IaaS Proof of Concept](https://gitlab.com/morph027/pve-infra-poc/blob/master/run.yml).
