RnodeC.rke2
=========

This role will deploy rke2.  Linux x86_64 only.  RKE2 version 1.25+ only (due to hardcoded cis-profile setting)

Requirements
------------

None   

Role Variables
--------------

The `rke2_role: server|agent` host or group var determines how the node gets deployed. So these should be configured in inventory or group_vars of your playbook.  Defaults to server. 

No other variables are required for this role.  See `defaults/main.yaml` for full list of available variables and their default values.  Here are a few that you might likely want to apply:

```
rke2_channel: stable
rke2_token: badsecret 
rke2_api_fqdn: rke2.local
```

If your nodes have more than one interface, and you want to specify which interface to use for cluster communications, set this variable (this assume):
```
rke_node_iface: eth1
```


#### rke2_channel 

This variable can be "stable", "latest", "testing", or a valid kubernetes major/minor version (i.e. "v1.24").  The ansible role will pull down the latest available rke2 version that corresponds to the channel.  

Alternatively, you can use an explicit rke2_version, such as `rke2_version: v1.23.17+rke2r1`

Dependencies
------------

None

Example Playbook
----------------

```bash
---
- name: Setup rke2 cluster
  hosts: cluster

  vars: 
    rke2_channel: 'v1.25'
  
  roles:
  - role: RnodeC.rke2 
```

If this is an upgrade scenario, be sure to set `--tags upgrade` in your ansible command. This will trigger a rolling restart of the cluster.  


Author Information
------------------

RnodeC - rnodec.io 