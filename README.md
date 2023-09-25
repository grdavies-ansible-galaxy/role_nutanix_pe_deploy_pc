# Nutanix Role to deploy Prism Central

This Ansible role downloads and deploys Nutanix Prism Central.

## Requirements

The following roles need to be installed as they are used within this role;

- grdavies.role_nutanix_prism_initial_password
- grdavies.role_nutanix_prism_eula
- grdavies.role_nutanix_prism_pulse
- grdavies.role_nutanix_pe_container_search

```
ansible-galaxy install grdavies.role_nutanix_prism_initial_password --force
ansible-galaxy install grdavies.role_nutanix_prism_eula --force
ansible-galaxy install grdavies.role_nutanix_prism_pulse --force
ansible-galaxy install grdavies.role_nutanix_pe_container_search --force
```

## Role Variables

| Variable                                    | Required | Default         | Choices                                                                         | Comments                                                                                                                                                                                                                                 |
|---------------------------------------------|----------|-----------------|---------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| nutanix_host                                | yes      |                 |                                                                                 | The IP address or FQDN for the Prism (Element only) to which you want to connect.                                                                                                                                                        |
| nutanix_username                            | yes      |                 |                                                                                 | A valid username with appropriate rights to access the Nutanix API.                                                                                                                                                                      |
| nutanix_password                            | yes      |                 |                                                                                 | A valid password for the supplied username.                                                                                                                                                                                              |
| nutanix_port                                | no       | 9440            |                                                                                 | The Prism TCP port.                                                                                                                                                                                                                      |
| validate_certs                              | no       | no              | yes | no                                                                        | Whether to check if Prism UI certificates are valid.                                                                                                                                                                                     |
| nutanix_pc_vip                              | no       | ''              |                                                                                 | Required if a scale-out Prism Central deployment is performed, otherwise this variable should be omitted.                                                                                                                                |
| nutanix_pc_deploy_vm_list                   | yes      | []              |                                                                                 | List of Prism Central VM name and IP address in a list format. Provide 1 entry for a standard Prism Central deployment, provide 3 entries for a scale-out Prism Central deployment. [{ "name": "pcvm1", "ip_address": "192.168.10.41" }] |
| nutanix_pc_deploy_size                      | no       | small           | ['small', 'large', 'xlarge']                                                   | See https://portal.nutanix.com/page/documents/details?targetId=Release-Notes-Prism-Central-vpc_2022_6:top-pc-scalability-r.html for details on PC sizing.                                                                                |
| nutanix_pc_deploy_container_name            | yes      |                 |                                                                                 | The name of the container (datastore) upon with to place the PC VM                                                                                                                                                                       |
| nutanix_pc_deploy_subnet_name               | yes      |                 |                                                                                 | The name of the subnet (port-group) upon with to place the PC VM                                                                                                                                                                         |
| nutanix_pc_deploy_network_address           | yes      |                 |                                                                                 | Network address for the above subnet using the following notation (10.10.10.0/24)                                                                                                                                                        |
| nutanix_pc_deploy_gateway                   | yes      |                 |                                                                                 | IPv4 gateway  for the above subnet                                                                                                                                                                                                       |
| nutanix_pc_deploy_dns_list                  | no       | []              |                                                                                 | List of DNS servers ['8.8.8.8', '8.8.4.4']                                                                                                                                                                                               |
| nutanix_pc_deploy_version                   | no       |                 |                                                                                 | If not provided the latests Prism Central version for the clusters AOS version will be deployed.                                                                                                                                         |
| nutanix_pc_deploy_pulse                     | no       | True            | True | False                                                                    |                                                                                                                                                                                                                                          |
| nutanix_pc_deploy_eula_accept               | no       | False           | True | False                                                                    | If ELUA is set to True the full_name, company and role variables are mandatory.                                                                                                                                                          |
| nutanix_pc_deploy_eula_full_name            | no       |                 |                                                                                 |                                                                                                                                                                                                                                          |
| nutanix_pc_deploy_eula_company_name         | no       |                 |                                                                                 |                                                                                                                                                                                                                                          |
| nutanix_pc_deploy_eula_job_title            | no       |                 |                                                                                 |                                                                                                                                                                                                                                          |

## Dependencies

- grdavies.role_nutanix_prism_initial_password
- grdavies.role_nutanix_prism_eula
- grdavies.role_nutanix_prism_pulse
- grdavies.role_nutanix_pe_container_search

## Example Playbook to deploy a single Prism Central instance

```
- hosts: localhost
  gather_facts: false
  roles:
    - role: grdavies.role_nutanix_pe_deploy_pc
  vars:
    nutanix_host: 10.38.185.37
    nutanix_username: admin
    nutanix_password: nx2Tech165!
    nutanix_pc_deploy_container_name: Default
    nutanix_pc_deploy_subnet_name: Primary
    nutanix_pc_deploy_network_address: 10.38.185.0/25
    nutanix_pc_deploy_gateway: 10.38.185.1
    nutanix_pc_deploy_vm_list:
      - name: pcvm1
        ip_address: 10.38.30.39
    nutanix_pc_deploy_dns_list:
      - 8.8.8.8
      - 8.8.4.4
    nutanix_pc_deploy_new_password: nx2Tech165!
    nutanix_pc_deploy_default_eula_accept: True
    nutanix_pc_deploy_default_eula_full_name: Ross Davies
    nutanix_pc_deploy_default_eula_company_name: Nutanix
    nutanix_pc_deploy_default_eula_job_title: SE
```

## Example Playbook to deploy a scale-out Prism Central instance

```
- hosts: localhost
  gather_facts: false
  roles:
    - role: grdavies.role_nutanix_pe_deploy_pc
  vars:
    nutanix_host: 10.38.26.37
    nutanix_username: admin
    nutanix_password: nx2Tech407!
    nutanix_debug: true
    nutanix_pc_deploy_container_name: Default
    nutanix_pc_deploy_subnet_name: Primary
    nutanix_pc_deploy_network_address: 10.38.26.0/25
    nutanix_pc_deploy_gateway: 10.38.26.1
    nutanix_pc_vip: 10.38.26.40
    nutanix_pc_deploy_vm_list:
      - name: pcvm1
        ip_address: 10.38.26.41
      - name: pcvm2
        ip_address: 10.38.26.42
      - name: pcvm3
        ip_address: 10.38.26.43
    nutanix_pc_deploy_dns_list:
      - 10.42.194.10
    nutanix_pc_deploy_new_password: nx2Tech407!
    nutanix_pc_deploy_eula_accept: true
    nutanix_pc_deploy_eula_full_name: Ross Davies
    nutanix_pc_deploy_eula_company_name: Nutanix
    nutanix_pc_deploy_eula_job_title: SE
    nutanix_pc_deploy_size: x-large
```


## License

See LICENSE.md

## Author Information

Ross Davies
/Users/ross.davies/Documents/GitHub/nutanix_ansible_galaxy_roles/role_nutanix_pe_deploy_pc/README.md