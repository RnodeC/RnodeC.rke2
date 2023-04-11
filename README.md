RnodeC.rke2
=========

This role will deploy rke2.  (linux amd64 only) 

Requirements
------------

At least a single vm.  The `rke2_role: server|agent` host or group var determines how the node gets deployed.  

Role Variables
--------------

No variables are required for this role.  See `defaults/main.yaml` for full list of available variables and their default values.  Here are a few that you might likely want to apply:

```
rke2_channel: stable
rke2_token: badsecret 
rke2_api_hostname: server
rke2_api_domain: local
```

#### rke2_channel 

This variable can be "stable", "latest", "testing", or a valid kubernetes major/minor version (i.e. "v1.24").  The ansible role will pull down the latest available rke2 version that corresponds to the channel.  

Dependencies
------------
None.  Optionally, apply the `RnodeC.lockdown` role (**coming soon**) to apply some added security to the cluster nodes.  

Example Playbook
----------------

```bash
---
- name: Setup rke2 cluster
  hosts: cluster

  vars: 
    rke2_channel: 'v1.24'
  
  roles:
  - role: RnodeC.rke2 
```

If this is an upgrade scenario, be sure to set `--tags upgrade` in your ansible command. This will trigger a rolling restart of the cluster.  


Author Information
------------------

RnodeC - rnodec.io 