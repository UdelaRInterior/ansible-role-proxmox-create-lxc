# Change Log

## [v3.0.1](https://github.com/UdelaRInterior/ansible-role-proxmox-create-lxc/tree/v3.0.1)

* missing namspace prefix and bad replacement that prevented setting any additional conf, 

## [v3.0.0](https://github.com/UdelaRInterior/ansible-role-proxmox-create-lxc/tree/v3.0.0)

* New interface with all role variables defined in the `pve_lxc_*` namespace. Update your host variables in your ansible code!
* Backwards compatibility with [previous interface](https://github.com/UdelaRInterior/ansible-role-proxmox-create-lxc/blob/v2.2.0/README.md#role-variables) up to v4.X.Y release 

## [v2.2.0](https://github.com/UdelaRInterior/ansible-role-proxmox-create-lxc/tree/v2.2.0)

* Now you can change the timeout for operations of the Ansible module `proxmox` according to the performance of your remote host. `proxmox_create_lxc_timeout` variable was added for this purpose.

## [v2.1.0](https://github.com/UdelaRInterior/ansible-role-proxmox-create-lxc/tree/v2.1.0)

* New variable `pve_additional_conf`. Simple list to indicate lines that should be added to the container configuration file ( `{{VMID}}.conf` ) at the time of creation. This is because they aren't possible configurations through the API and the Ansible's Proxmox module.

## [v2.0.0](https://github.com/UdelaRInterior/ansible-role-proxmox-create-lxc/tree/v2.0.0)

* New variables: `net_interfaces` and `mounts`. Network interfaces and additional mount points from a yaml list without quantity limitations (will depend on the maximum supported by the Proxmox server).

  Deprecated vars: `netif_name`, `netif_ip4`, `netif_netmask`, `netif_gw`, `netif_ip6`, `netif_netmask6`, `netif_gw6`, `netif_bridge`.

## [v1.0.0](https://github.com/UdelaRInterior/ansible-role-proxmox-create-lxc/tree/v1.0.0)

* First stable version. Creation of only one network interface and up to three additional mounting points are contemplated.