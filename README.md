Ansible role proxmox_create_lxc
=========

 A complete role for LXC container creation in a Proxmox Virtual Environement (pve) cluster, with network fulluy configured and eventually several disks with acl and quotas management. 

Requirements
------------

You must act on a proxmox node or cluster already configured, i.e. you need Proxmox Virtual Environement (pve) node already installed (tested with pve 5), and a proxmox user with lxc container creation rights. 

Yo also need an ssh key configured in the local machine, where ansible is ran, i.e. a file `~/.ssh/id_rsa.pub`. 


Installation
------------

To be able to update later and eventually to modify it, prefer using `requirements.yml` with the git source:

```yaml
- name: create_lxc
  src: https://github.com/UdelaRInterior/ansible-role-proxmox-create-lxc.git
  ```
And then download it with `ansible-galaxy`:

```shell
$ ansible-galaxy install -r requirements.yml -g
```

Using `git`, you'll have to be carefull to folder name :

```shell
$ git clone https://github.com/UdelaRInterior/ansible-role-proxmox-create-lxc.git create_lxc
```

Role Variables
--------------

The `defaults` variables define the container parameters. To be specified by host under `host_vars/host_fqdn/vars` and eventually encrypted in `host_vars/host_fqdn/vault`

```yaml
node: my_node
api_host: my_node.my_cluster.org
api_user: deploy@pam
## better put this in a vautl, ex host_vars/inventory_hostname/vault/main.yml
## we have to find a way to define node_deploy_password as node[hostname]/deploy_password
node_deploy_password: D3pl0y_pwd
url_ostemplate: http://download.proxmox.com/images/system/debian-9.0-standard_9.5-1_amd64.tar.gz
cores: 1
memory: 512
storage: local-lvm
disk: 30
netif_name: eth0
# esta variable puede estar definida o no
# this variable may be defined or not
#netif_hwaddr: 52:54:00:00:a3:a0
# Do define at least the IPv4!
netif_ip4: 192.168.33.10
netif_netmask: 24
netif_gw: 192.168.33.1
netif_ip6: 2001:db8::10
netif_netmask6: 64
netif_gw6: 2001:db8:6a::1
netif_bridge: vmbr0
nameserver: 192.168.8.8
## better put this in a vautl, ex host_vars/inventory_hostname/vault/main.yml
root_password: 123probando123
```

Dependencies
------------

We need ansible version > 2.5 (?) to have the appropriate API of proxmox modules. 

Proxmox pve 5 installed in a the proxmox node. 

Example Playbook
----------------

Let's say, as the vars example, `my_node.my_cluster.org` is our pve node NS (api on port 8006), `my_node` it's pve node name, and `deploy` our proxmox user in this node, and `pve_containers_group` an ansible group of the containers to define. 

Given that: 
* containers are named `<container>.node.my_cluster.org`, 
* Name resolutions of pve containers and node are configured, 
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

BE CAREFULL: contrairly to debian standard installation, proxmox container templates let remote root ssh open. 

License
-------

(c) Universidad de la República (UdelaR), Red de Unidades Informáticas de la UdelaR en el Interior. 
licenced under GPL-v3

Author Information
------------------

Ulvida (daniel@uruguayos.fr)
https://github.com/UdelaRInterior
https://proyectos.interior.edu.uy/

Inspired in [this Proxmox IaaS Proof of Concept](https://gitlab.com/morph027/pve-infra-poc/blob/master/run.yml). 
