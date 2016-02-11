ansible-dse
---------

These Ansible playbooks will build a DataStax Enterprise cluster.

You can pre-build a Rackspace cloud environment or run the playbooks against an existing environment.

It support multiple Rackspace Cloud Regions, multiple virtual datacenters per Region (to separate workloads) and static / dedicated inventory.

The data drive can be customized and can be put on top of Rackspace Cloud Block Storage.


## [Requirements] (id:requirements)

- Ansible >= 2.0.1

- Expects CentOS 7 or Ubuntu 14

- Building the cloud environment requires the `pyrax` Python module: https://github.com/rackspace/pyrax


## [Configuration] (id:configuration)
  
A single configuration file, `playbooks/groupvars/all` is used to set global variables and the topology.

- replace user and pass with your datastax credentials (used when signing up at https://academy.datastax.com/user/login):
```
     dserepouser: 'user'
     dserepopass: 'pass'
```

- set the cluster name
 
- set the virtual datacenter where opscenter runs. It can be on a separate datacenter or one shared with DSE nodes.

  If opscenter is installed in a virtual datacenter shared with DSE, it will be installed on the first node.

- replace the `listen_interface`, `broadcast_interface` and `rpc_interface` with the network device names from the hosts:
```
     listen_interface: 'eth1'
     broadcast_interface: 'eth0'
     rpc_interface: 'eth1'
```

- for multi-region deployments, `broadcast_interface` must be set to a reachable interface from the other regions.

- set the workloads for each virtual datacenter.

- set cloud options if Cloud Servers need to be built.

    
## [Inventory] (id:configuration)

- The cloud environment requires the standard pyrax credentials file that looks like this:
  ````
  [rackspace_cloud]
  username = my_username
  api_key = 01234567890abcdef
  ````
  
  This file will be referenced in the `RAX_CREDS_FILE` environment variable (or `RAX_LON_CREDS_FILE` for the LON region).

  By default, the file is expected to be: `~/.raxpub` and if you use LON region, `~/.raxpub-uk` must also be set.


- When provisioning DSE on existing infrastructure edit `inventory/static` to include all hosts you expect to install DSE on and assign the hosts to the groups configured in the topology.


## [Installation] (id:installation)

To provision a cloud environment, run the `provision_cloud.sh` script:

````
bash provision_cloud.sh
````

Then run the bootstrap and dse scripts (in this order):
````
bash bootstrap.sh
bash dse.sh
````

## [OpsCenter] (id:opscenter)

OpsCenter will be installed on the first node from the virtual datacenter assigned to it.

The provided Ansible playbook will only open the firewall if you've added your workstation IP to `allowed_external_ips` variable in the `playbooks/group_vars/all` file. 

Alternatively, you can access OpsCenter by either opening the firewall manually or by opening a socks proxy with the following command:

```
ssh -D 12345 root@{{ opscenter-node }}
```

Configure a browser to use localhost port 12345 as socks proxy.

You'll then be able to navigate to `http://opscenter-node:8888` in your configured browser and access all subsidiary links.
