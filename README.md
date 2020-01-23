idm
=========

This role installs and configures RHEL Identity Manager (IdM).

> NOTE: This role will be deprecated in favor of the roles available in the [FreeIPA collection](https://github.com/freeipa/ansible-freeipa)

Requirements
------------

- Expects a working RHEL 7 system to target
- Red Hat Network account with a RHEL subscription available

Role Variables
--------------

| Variable        | Required | Default  | Description                                                                                                                                                                                                                                     |
| --------------- | -------- | -------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `domain` | :x:      | ```hattrick.lab``` | The domain for the environment |
| `dns_server_public` | :x:      | ```1.1.1.1``` | The default upstream DNS server to use |
| `idm_hostname` | :heavy_check_mark:      |  | The short hostname for IdM |
| `idm_ssh_user` | :x:      | ```root``` | The default user to use for SSH access to IdM |
| `idm_ssh_pwd` | :x:      | ```p@ssw0rd``` | The default password to use for SSH access to IdM. Obviously you'd change this :) |
| `idm_public_ip` | :heavy_check_mark:      |  | The reachable public IP for IdM |
| `idm_repos` | :x:      | ```see defaults/main.yml``` | Dictionary of Repos to enable for IdM |
| `idm_packages` | :x:      | ```see defaults/main.yml``` | Dictionary of Packages to create for IdM |
| `idm_realm` | :heavy_check_mark:      |  | Identity Realm for IdM (ex: HATTRICK.LAB) |
| `idm_dm_pwd` | :heavy_check_mark:      |  | Identity Realm for IdM (ex: HATTRICK.LAB) |
| `idm_admin_pwd` | :heavy_check_mark:      |  | Password for admin user for IdM |
| `idm_forward_ip` | :heavy_check_mark:      | ```{{ dns_server_public }}```  | IP of Upstream DNS to set as the forwarder (for disconnected, don't set a forward IP) |
| `idm_reverse_zone` | :heavy_check_mark:      |  | Reverse zone to create in IdM (ex: "168.192.in-addr.arpa.") |
| `idm_users` | :heavy_check_mark:      |  | Dictionary of users to create in IdM post configuration |
| `idm_dns_records` | :heavy_check_mark:      |  | Dictionary of DNS records to create in IdM post configuration |
| `idm_domain` | :x: | ```{{ domain }}``` | The domain for the IDM server |
| `idm_reverse_zones` | :x:      | ```see defaults/main.yml``` | List of all reverse zones to create |
| `idm_forward_zones` | :x:      | ```see defaults/main.yml``` | List of all forward zones to create |
| `idm_idstart` | :x:      | ```see defaults/main.yml``` | (--idstart) The starting user and group id number  |
| `idm_idmax` | :x:      | ```see defaults/main.yml``` | (--idmax) The  maximum  user  and  group  id  number |
| `idm_mkhomedir` | :x:      | ```see defaults/main.yml``` | (--mkhomedir) |
| `idm_setup_dns` | :x:      | ```see defaults/main.yml``` | (--setup-dns) |
| `idm_ssh_trust_dns` | :x:      | ```see defaults/main.yml``` | (--ssh-trust-dns) Configure OpenSSH client to trust DNS SSHFP records. |
| `idm_hbac_allow` | :x:      | ```see defaults/main.yml``` | (--no-hbac-allow) Don't install allow_all HBAC rule |
| `idm_setup_ntp` | :x:      | ```see defaults/main.yml``` | Set to Flase to set (--no-ntp) |
| `idm_configure_ssh` | :x:      | ```see defaults/main.yml``` | Set to false to disable ssh client (--no-ssh) |
| `idm_configure_sshd` | :x:      | ```see defaults/main.yml``` | Set to False to not configure the SSH server (--no-sshd) |
| `idm_ui_redirect` | :x:      | ```see defaults/main.yml``` | Set to False to not redirect to UI (--no-ui-redirect)|
| `idm_host_dns` | :x:      | ```see defaults/main.yml``` | Do not use DNS for hostname lookup during install (--no-host-dns) |
| `idm_auto_reverse` | :x:      | ```see defaults/main.yml``` | Creates reverse zone if not exist (--auto-reverse) |
| `idm_setup_kra` | :x:      | ```see defaults/main.yml``` | Set to true to install secret service (--setup-kra) |
| `idm_zone_overlap` | :x:      | ```see defaults/main.yml``` | Create zone if it already exists (--allow-zone-overlap) |
| `idm_zones` | :x:      | ```{{ idm_reverse_zones }},{{ idm_forward_zones }}``` | Sets up array of all zones |


Dependencies
------------

- RedHatGov.rhsm

Example Playbook
----------------

```yaml
---
- hosts: idm
  tags: install
  vars:
    domain: "example.com"
    dns_server_public: 1.1.1.1
    idm_hostname: idm #Short hostname
    idm_ssh_user: root
    idm_ssh_pwd: redhat
    idm_public_ip: "192.168.0.4"
    idm_repos:
      - rhel-7-server-rpms
      - rhel-7-server-extras-rpms
      - rhel-7-server-optional-rpms
    idm_packages:
      - ipa-server
      - ipa-server-dns
    idm_realm: "{{ domain | upper }}"
    idm_dm_pwd: "Redhat1993"
    idm_admin_pwd: "Redhat1993"
    idm_forward_ip: "{{ dns_server_public }}"
    idm_reverse_zone: "168.192.in-addr.arpa."
    idm_users:
       - username: operator
         password: redhat1234
         display_name: "Operator"
         first_name: Oper
         last_name: Ator
         email: "operator@redhat.com"
         phone: "+18887334281"
         title: "Systems Administrator"
    idm_dns_records:
       - hostname: router
         record_type: A
         ip_address: 192.168.0.1
         reverse_zone: "{{ idm_reverse_zone }}"
         reverse_record: 1.0
       - hostname: switch
         record_type: A
         ip_address: 192.168.0.2
         reverse_zone: "{{ idm_reverse_zone }}"
         reverse_record: 2.0
       - hostname: kvm
         record_type: A
         ip_address: 192.168.0.3
         reverse_zone: "{{ idm_reverse_zone }}"
         reverse_record: 3.0
  tasks:
    - name: Install IDM
      include_role:
        name: idm
      tags: [install,preinstall,installer,firewall,always,result]

    - name: Configure IDM
      include_role:
        name: idm
        tasks_from: post_config
      tags: [install,preinstall,installer,firewall,always,result]
```

License
-------

GPLv3

Author Information
------------------

[Red Hat North American Public Sector Solution Architects](https://redhatgov.io)
