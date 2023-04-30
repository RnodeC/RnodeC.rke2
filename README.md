RnodeC.rke2
=========

This role will deploy rke2.   RKE2 version 1.25+ only (*due to hardcoded cis-profile setting*)

Requirements
------------

Debian or RedHat Linux x86_64

Role Variables
--------------

The `rke2_role: server|agent` host or group var determines how the node gets deployed. So these should be configured in inventory or group_vars of your playbook.  Defaults to server. 

No other variables are required for this role.  See `defaults/main.yaml` for full list of available variables and their default values.  Here are a few that you might likely want to apply:

```
rke2_channel: stable
rke2_api_fqdn: rke2.local
```

If your nodes have more than one interface, and you want to specify which interface to use for cluster communications, set this variable:
```
rke2_node_iface: eth2
```
#### rke2_channel 

This variable can be "stable", "latest", "testing", or a valid kubernetes major/minor version (i.e. "v1.24").  The ansible role will pull down the latest available rke2 version that corresponds to the channel.  

Alternatively, you can use an explicit rke2_version, such as `rke2_version: v1.23.17+rke2r1`

#### rke2_longhorn ###


Dependencies
------------

None

Example Playbook
----------------

Simple Example:
```bash
---
- name: Setup rke2 cluster
  hosts: cluster

  vars: 
    rke2_channel: 'v1.25'
  
  roles:
  - role: RnodeC.rke2 
```

Little more:
```bash
- name: RKE2
  hosts: all 

  # ansible_user, ansible_port, etc can go in here
  vars_files: ansible-vars.yaml

  roles:
  - role: RnodeC.keepalived
    vars:
      keepalived_fqdn: rke2.local
      keepalived_vip: 192.168.1.101
      keepalived_rke2_enabled: true
  
  - role: RnodeC.rke2
    vars:
      rke2_api_fqdn: rke2.local
      rke2_api_ip: 192.168.1.101
      rke2_channel: 'v1.25'
      rke2_local_storage: true
      rke2_local_storage_path: /mnt/data/rke2

  post_tasks:
  - name: Retrieve kubeconfig
    ansible.builtin.fetch:
      src: /etc/rancher/rke2/rke2.yaml
      dest: "{{ playbook_dir }}/kubeconfig"
      flat: true
    delegate_to: "{{ groups['servers'][0] }}"
    run_once: true
    become: true

  - name: Update kubeconfig with vip fqdn
    ansible.builtin.replace:
      path: "{{ playbook_dir }}/kubeconfig"
      regexp: '^(\s+server:).*$'
      replace: '\1 https://{{ rke2_api_fqdn }}:6443'
    delegate_to: localhost

```

> If this is an upgrade scenario, be sure to set `--tags upgrade` in your ansible command. This will trigger a rolling restart of the cluster.  


Author Information
------------------

RnodeC - rnodec.io 