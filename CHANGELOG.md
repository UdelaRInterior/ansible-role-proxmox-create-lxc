# Change Log

## Collection `cielito.proxmox` [v1.0.0](https://git.interior.edu.uy/cielito/proxmox/tree/v1.0.0)

The role is migrated to the [`cielito.proxmox` collection](https://git.interior.edu.uy/cielito/proxmox/), this repository is deprecated, available read only for archives purposes. 


## [v5.1.0](https://github.com/UdelaRInterior/ansible-role-proxmox-create-lxc/tree/v5.1.0)

## [v5.0.0](https://github.com/UdelaRInterior/ansible-role-proxmox-create-lxc/tree/v5.0.1)

* `pvesh get nodes/{{ pve_node }}/lxc` is now used to extract the container's VMID instead of the `pct list` command. Previously, if a container was created on `pve_node` != `pve_api_host`, it was impossible to extract the container VMID from the Proxmox node used to connect via API. Now with `pvesh get` it is possible.

## [v5.0.2](https://github.com/UdelaRInterior/ansible-role-proxmox-create-lxc/tree/v5.0.2)

* Installing the `python3-pip` package directly from apt instead of the ambiguous `python-pip`. This change allows the role to run correctly on all Proxmox versions supported (PVE >= 5), since `python3-pip` is available on all of them, but `python-pip` is not on the latest ones.

## [v5.0.1](https://github.com/UdelaRInterior/ansible-role-proxmox-create-lxc/tree/v5.0.1)

* Full alignment of namespace with proxmox_create_kvm roll. (API changes, but let's say its a fix of the API and maintain the major version number)
* No default values for optional variables
* Add CI workflow in GH Actions

## [v5.0.0](https://github.com/UdelaRInterior/ansible-role-proxmox-create-lxc/tree/v5.0.0)

Updated to Ansible v2.10 (use of collections) and several improvements:
* New method to import OS templates from the official repository provided by Proxmox, idempotent for directly using the `community.general.proxmox_template` module. The old method (reimplemented) is kept as a secondary option, which also allows the use of own or third-party template repositories.
* Additional configurations are now idempotent and applicable to an existing container with the new variable `pve_lxc_additional_configurations`.
* Implemented authentication in the PVE node via tokens.
* Now there is a variable associated with each of the Proxmox modules parameters.
* Tags `download` and` descarga` renamed to `pve_download` and` pve_descarga` respectively to avoid overlapping with other playbooks and roles due to being such a generic term.
* Tag `deploy` removed for unclear use case.
* Avoided with variables the use of numerous previously hardcoded and assumed values.
* Numerous variables renamed to be mnemonic.
* Unification of criteria for variable name prefixes.

## [v4.0.0](https://github.com/UdelaRInterior/ansible-role-proxmox-create-lxc/tree/v4.0.0)

* End of v2.0.0 variables' API backward's compatibility, no longer needed and not considered clean code.
* The new variable's API no longer provides a default value of root's container password (`pve_lxc_root_password`) to avoid unsecure container creation.

## [v3.0.1](https://github.com/UdelaRInterior/ansible-role-proxmox-create-lxc/tree/v3.0.1)

* Missing namspace prefix and bad replacement that prevented setting any additional conf.

## [v3.0.0](https://github.com/UdelaRInterior/ansible-role-proxmox-create-lxc/tree/v3.0.0)

* New interface with all role variables defined in the `pve_lxc_*` namespace. Update your host variables in your ansible code!
* Backwards compatibility with [previous interface](https://github.com/UdelaRInterior/ansible-role-proxmox-create-lxc/blob/v2.2.0/README.md#role-variables) up to v4.X.Y release.

## [v2.2.0](https://github.com/UdelaRInterior/ansible-role-proxmox-create-lxc/tree/v2.2.0)

* Now you can change the timeout for operations of the Ansible module `proxmox` according to the performance of your remote host. `proxmox_create_lxc_timeout` variable was added for this purpose.

## [v2.1.0](https://github.com/UdelaRInterior/ansible-role-proxmox-create-lxc/tree/v2.1.0)

* New variable `pve_additional_conf`. Simple list to indicate lines that should be added to the container configuration file ( `{{VMID}}.conf` ) at the time of creation. This is because they aren't possible configurations through the API and the Ansible's Proxmox module.

## [v2.0.0](https://github.com/UdelaRInterior/ansible-role-proxmox-create-lxc/tree/v2.0.0)

* New variables: `net_interfaces` and `mounts`. Network interfaces and additional mount points from a yaml list without quantity limitations (will depend on the maximum supported by the Proxmox server).

  Deprecated vars: `netif_name`, `netif_ip4`, `netif_netmask`, `netif_gw`, `netif_ip6`, `netif_netmask6`, `netif_gw6`, `netif_bridge`.

## [v1.0.0](https://github.com/UdelaRInterior/ansible-role-proxmox-create-lxc/tree/v1.0.0)

* First stable version. Creation of only one network interface and up to three additional mounting points are contemplated.