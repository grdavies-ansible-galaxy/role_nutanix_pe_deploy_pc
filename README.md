# Nutanix Role to deploy Prism Central

This Ansible role downloads and deploys Nutanix Prism Central.

## Requirements

The following roles need to be installed as they are used within this role;

- grdavies.role_nutanix_prism_initial_password
- grdavies.role_nutanix_prism_eula
- grdavies.role_nutanix_prism_pulse
- grdavies.role_nutanix_pe_container_search

```bash
ansible-galaxy install grdavies.role_nutanix_prism_initial_password --force
ansible-galaxy install grdavies.role_nutanix_prism_eula --force
ansible-galaxy install grdavies.role_nutanix_prism_pulse --force
ansible-galaxy install grdavies.role_nutanix_pe_container_search --force
```

## Input Variables

| Variable                                            | Required | Default                                 | Choices                                                                         | Comments                                                                                                                                                                                                                                 |
|-----------------------------------------------------|----------|-----------------------------------------|---------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| role_nutanix_pe_deploy_pc_host                      | yes      |                                         |                                                                                 | The IP address or FQDN for the Prism (Element only) to which you want to connect.                                                                                                                                                        |
| role_nutanix_pe_deploy_pc_host_username             | yes      |                                         |                                                                                 | A valid username with appropriate rights to access the Nutanix API.                                                                                                                                                                      |
| role_nutanix_pe_deploy_pc_host_password             | yes      |                                         |                                                                                 | A valid password for the supplied username.                                                                                                                                                                                              |
| role_nutanix_pe_deploy_pc_host_port                 | no       | 9440                                    |                                                                                 | The Prism TCP port.                                                                                                                                                                                                                      |
| role_nutanix_pe_deploy_pc_host_validate_certs       | no       | false                                   | true | false                                                                    | Whether to check if Prism UI certificates are valid.                                                                                                                                                                                     |
| role_nutanix_pe_deploy_pc_debug                     | no       | false                                   | true / false                                                                    | Enable debug logging                                                                                                                                                                                                                     |
| role_nutanix_pe_deploy_pc_vip                       | no       | ''                                      |                                                                                 | Required if a scale-out Prism Central deployment is performed, otherwise this variable should be omitted.                                                                                                                                |
| role_nutanix_pe_deploy_pc_vm_list                   | yes      | []                                      |                                                                                 | List of Prism Central VM name and IP address in a list format. Provide 1 entry for a standard Prism Central deployment, provide 3 entries for a scale-out Prism Central deployment. [{ "name": "pcvm1", "ip_address": "192.168.10.41" }] |
| role_nutanix_pe_deploy_pc_deploy_size               | no       | small                                   | ['small', 'large', 'xlarge']                                                    | See https://portal.nutanix.com/page/documents/details?targetId=Release-Notes-Prism-Central-vpc_2022_6:top-pc-scalability-r.html for details on PC sizing.                                                                                |
| role_nutanix_pe_deploy_pc_container_name            | yes      |                                         |                                                                                 | The name of the container (datastore) upon with to place the PC VM                                                                                                                                                                       |
| role_nutanix_pe_deploy_pc_subnet_name               | yes      |                                         |                                                                                 | The name of the subnet (port-group) upon with to place the PC VM                                                                                                                                                                         |
| role_nutanix_pe_deploy_pc_network_address           | yes      |                                         |                                                                                 | Network address for the above subnet using the following notation (10.10.10.0/24)                                                                                                                                                        |
| role_nutanix_pe_deploy_pc_gateway                   | yes      |                                         |                                                                                 | IPv4 gateway  for the above subnet                                                                                                                                                                                                       |
| role_nutanix_pe_deploy_pc_dns_list                  | no       | []                                      |                                                                                 | List of DNS servers ['8.8.8.8', '8.8.4.4']                                                                                                                                                                                               |
| role_nutanix_pe_deploy_pc_version                   | no       |                                         |                                                                                 | If not provided the latests Prism Central version for the clusters AOS version will be deployed.                                                                                                                                         |
| role_nutanix_pe_deploy_pc_password                  | no       | role_nutanix_pe_deploy_pc_host_password |                                                                                 | If not provided the latests Prism Central version for the clusters AOS version will be deployed.                                                                                                                                         |
| role_nutanix_pe_deploy_pc_pulse                     | no       | true                                    | true | false                                                                    |                                                                                                                                                                                                                                          |
| role_nutanix_pe_deploy_pc_eula_accept               | no       | false                                   | true | false                                                                    | If ELUA is set to True the full_name, company and role variables are mandatory.                                                                                                                                                          |
| role_nutanix_pe_deploy_pc_eula_full_name            | no       |                                         |                                                                                 |                                                                                                                                                                                                                                          |
| role_nutanix_pe_deploy_pc_eula_company_name         | no       |                                         |                                                                                 |                                                                                                                                                                                                                                          |
| role_nutanix_pe_deploy_pc_eula_job_title            | no       |                                         |                                                                                 |                                                                                                                                                                                                                                          |
| role_nutanix_pe_deploy_pc_override_vcpu             | no       |                                         |                                                                                 | Override value for PC VM vCPU                                                                                                                                                                                                            |
| role_nutanix_pe_deploy_pc_override_disk_gb          | no       |                                         |                                                                                 | Override value for PC VM disk in GB                                                                                                                                                                                                      |
| role_nutanix_pe_deploy_pc_override_vram_gb          | no       |                                         |                                                                                 | Override value for PC VM RAM in GB                                                                                                                                                                                                       |

## Dependencies

- grdavies.role_nutanix_prism_api
- grdavies.role_nutanix_prism_monitor_task
- grdavies.role_nutanix_prism_initial_password
- grdavies.role_nutanix_prism_eula
- grdavies.role_nutanix_prism_pulse

## Example Playbook to deploy a single Prism Central instance

```YAML
- hosts: localhost
  gather_facts: false
  roles:
    - role: grdavies.role_nutanix_pe_deploy_pc
  vars:
    role_nutanix_pe_deploy_pc_host: 10.38.11.199
    role_nutanix_pe_deploy_pc_host_username: admin
    role_nutanix_pe_deploy_pc_host_password: nx2Tech682!
    role_nutanix_pe_deploy_pc_debug: true
    role_nutanix_pe_deploy_pc_version: pc.2023.3
    role_nutanix_pe_deploy_pc_container_name: Default
    role_nutanix_pe_deploy_pc_subnet_name: Primary
    role_nutanix_pe_deploy_pc_network_address: 10.38.11.192/26
    role_nutanix_pe_deploy_pc_gateway: 10.38.11.193
    role_nutanix_pe_deploy_pc_dns_list:
      - 10.42.194.10
    nutanix_pc_deploy_new_password: nx2Tech682!
    role_nutanix_pe_deploy_pc_eula_accept: true
    role_nutanix_pe_deploy_pc_eula_full_name: Ross Davies
    role_nutanix_pe_deploy_pc_eula_company_name: Nutanix
    role_nutanix_pe_deploy_pc_eula_job_title: SE
    role_nutanix_pe_deploy_pc_deploy_size: xlarge
    role_nutanix_pe_deploy_pc_vm_list:
      - name: pcvm1
        ip_address: 10.38.11.250
```

## Example Playbook to deploy a scale-out Prism Central instance

```YAML
- hosts: localhost
  gather_facts: false
  roles:
    - role: grdavies.role_nutanix_pe_deploy_pc
  vars:
    role_nutanix_pe_deploy_pc_host: 10.38.11.199
    role_nutanix_pe_deploy_pc_host_username: admin
    role_nutanix_pe_deploy_pc_host_password: nx2Tech682!
    role_nutanix_pe_deploy_pc_debug: true
    role_nutanix_pe_deploy_pc_version: pc.2023.3
    role_nutanix_pe_deploy_pc_container_name: Default
    role_nutanix_pe_deploy_pc_subnet_name: Primary
    role_nutanix_pe_deploy_pc_network_address: 10.38.11.192/26
    role_nutanix_pe_deploy_pc_gateway: 10.38.11.193
    role_nutanix_pe_deploy_pc_dns_list:
      - 10.42.194.10
    role_nutanix_pe_deploy_pc_password: nx2Tech682!
    role_nutanix_pe_deploy_pc_eula_accept: true
    role_nutanix_pe_deploy_pc_eula_full_name: Ross Davies
    role_nutanix_pe_deploy_pc_eula_company_name: Nutanix
    role_nutanix_pe_deploy_pc_eula_job_title: SE
    role_nutanix_pe_deploy_pc_deploy_size: small
    role_nutanix_pe_deploy_pc_vip: 10.38.11.240
    role_nutanix_pe_deploy_pc_vm_list:
      - name: pcvm1
        ip_address: 10.38.11.241
      - name: pcvm2
        ip_address: 10.38.11.242
      - name: pcvm3
        ip_address: 10.38.11.243
```

## License

See LICENSE.md

## Author Information

Ross Davies
