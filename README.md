Ansible role proxmox_create_lxc
=========

[![Galaxy](https://img.shields.io/badge/galaxy-UdelaRInterior.proxmox__create__lxc-blue.svg)](https://galaxy.ansible.com/udelarinterior/proxmox_create_lxc) ![GitHub tag (latest by date)](https://img.shields.io/github/v/tag/udelarinterior/ansible-role-proxmox-create-lxc?label=release&logo=github&style=social) ![GitHub stars](https://img.shields.io/github/stars/udelarinterior/ansible-role-proxmox-create-lxc?style=social) ![GitHub forks](https://img.shields.io/github/forks/udelarinterior/ansible-role-proxmox-create-lxc?style=social)

A complete role for LXC container creation in a Proxmox Virtual Environement (PVE) cluster, with network fully configured and eventually several disks with acl and quotas management.

Requirements
------------

You must act on a Proxmox node or cluster already configured, i.e. you need Proxmox Virtual Environement (PVE) node already installed (tested with PVE 5), and a Proxmox user with LXC container creation rights.

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

The `defaults` variables define the container parameters. To be specified by host under `host_vars/host_fqdn/vars` and eventually encrypted in `host_vars/host_fqdn/vault`

```yaml
node: my_node
api_host: my_node.my_cluster.org
api_user: deploy@pam
node_deploy_password: D3pl0y_pwd
url_ostemplate: http://download.proxmox.com/images/system/debian-10.0-standard_10.0-1_amd64.tar.gz
unprivileged: true
cores: 1
cpu_limit: 1
cpu_units: 1000
memory: 512
swap: 512
disk: 32
storage: local-lvm
nameserver: 192.168.8.8 192.168.8.4
root_password: 123testing1234
onboot: no

net_interfaces:
  - id: net0
    name: eth0
    hwaddr: F6:A2:69:61:94:8D
    ip4: 192.168.33.10        # ip4: dhcp (to use DHCP)
    netmask4: 24
    gw4: 192.168.33.1
    ip6: 200:db8::10          # ip6: dhcp  (to use DHCP)  ### ip6: auto (to use SLAAC)
    netmask6: 64
    gw6: 200:db8::1
    bridge: vmbr0
    firewall: false  # Setting netif_firewall in TRUE, enable the use of firewall on the network interface
    rate_limit: 5    # In MB/s
    vlan_tag: 200
  - id: net1
    name: eth1
    hwaddr: C6:A5:19:B1:92:7D
    ip6: 200:db8::10          # ip6: dhcp  (to use DHCP)  ### ip6: auto (to use SLAAC)
    netmask6: 64
    bridge: vmbr1

mounts:
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

# You can change the timeout for the operations of the module according to the performance of your remote host
# proxmox_create_lxc_timeout: 30

# Additional "manual" settings to add to the file /etc/pve/nodes/{{ node }}/lxc/{{ VMID }}.conf
pve_additional_conf: []
        # Kernel modules available within the LXC
  # - 'mp0: /lib/modules/4.15.18-9-pve,mp=/lib/modules/4.15.18-9-pve,ro=1'
        # tun device for OpenVPN server inside LXC
  # - 'lxc.cgroup.devices.allow = c 10:200 rwm'
  # - 'lxc.hook.autodev = sh -c "modprobe tun; cd ${LXC_ROOTFS_MOUNT}/dev; mkdir net; mknod net/tun c 10 200; chmod 0666 net/tun"'
```

Dependencies
------------

We need Ansible version > 2.5 (?) to have the appropriate API of Proxmox modules.

Proxmox VE 5 installed in a the Proxmox node.

Example Playbook
----------------

Let's say, as the vars example, `my_node.my_cluster.org` is our pve node NS (api on port 8006), `my_node` it's pve node name, and `deploy` our Proxmox user in this node, and `pve_containers_group` an Ansible group of the containers to define.

Given that:
* containers are named `<container>.node.my_cluster.org`,
* Name resolutions of PVE containers and node are configured,
* containers' variables are defined (for example in each `host_vars/<container>/vars/`),
* All new IPs are allocated and routed,

the following playbook creates all the containers declared in the `pve_containers_group`,

    - name: create containers declared in  pve_containers_group
      hosts: pve_containers_group
      remote_user: deploy
      become: yes
      gather_facts: no

      roles:
        - create_lxc

will create and start the containers, and configure root access with the `root_password` defined and the ssh key of your local machine (`~/.ssh/id_rsa.pub`).

BE CAREFULL: contrairly to debian standard installation, Proxmox container templates let remote root SSH open.

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
