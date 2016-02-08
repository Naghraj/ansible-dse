# ansible-dse

Best Practices for installing Datastax Enterprise

Currently no disk formating occurs but will likely be added soon for provisioning a data dir

Instructions: 

  When provisioning DSE on existing infrastructure:
  - Edit inventory/hosts to include all hosts you expect to install DSE on

  - Edit playbooks/groupvars/all 
    
```
    name your cluster
     cluster_name: testcluster

    replace user and pass with your datastax credentials to use repo:
     dserepouser: 'user'
     dserepopass: 'pass'

    replace the listen_interface, broadcast_interface and rpc_interface with the required net device names
     listen_interface: 'eth1'
     broadcast_interface: 'eth0'
     rpc_interface: 'eth1'

    for multi-region deployments, broadcast_interface must be set to a reachable interface from the other regions

    JMX is not currently enabled in the code but here for future use
     jmxuser: cassandra
     jmxpass: cassandra
```
```
bash dse-dedicated.sh
```

To provision Rackspace public cloud use these steps:

To customize, change the variables under `playbooks/group_vars/all`:

modify the variables under `cloud` to control the number and flavor of nodes in your cluster

## [Requirements] (id:requirements)

- Ansible >= 2.0.1

- Expects CentOS 7

- Building the cloud environment requires the `pyrax` Python module: https://github.com/rackspace/pyrax

  Also recommended is to run `pip install oslo.config netifaces`.

- The cloud environment requires the standard pyrax credentials file that looks like this:
  ````
  [rackspace_cloud]
  username = my_username
  api_key = 01234567890abcdef
  ````
  
  This file will be referenced in `playbooks/group_vars/all` (the `rax_credentials_file` variable).

  By default, the file is expected to be: `~/.raxpub` and if you use LON, `~/.raxpub-uk` must also be set.

  You can specify your own script path by setting `RAX_CREDS_FILE` and `RAX_LON_CREDS_FILE` environment variables.

## [Scripts] (id:scripts)

To provision a cloud environment, run the `provision_cloud.sh` script after you've customized the variables under:

````
bash provision_cloud.sh
````
```
bash bootstrap.sh
bash dse.sh
```

## [Additionally] (id:additionally)

All firewall rules provided here allow the cluster to access itself and only allows ssh access from outside.

Opscenter is configured to be installed on the first server alpha-numerically in the list.

to access opscenter:
```
ssh -D 12345 root@{{ opscenter-node }}
```

configure a browser to use localhost port 12345 as socks proxy 

browse to the opscenter node ip port 8888

