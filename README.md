# pacemaker

This role is used to deploy pacemaker.

## Inventory file
```
pacemaker-1 ansible_ssh_host=10.10.10.11 ansible_ssh_port=3322 ansible_ssh_user=centos role=operator
pacemaker-2 ansible_ssh_host=10.10.10.12 ansible_ssh_port=3322 ansible_ssh_user=centos
pacemaker-3 ansible_ssh_host=10.10.10.13 ansible_ssh_port=3322 ansible_ssh_user=centos

[cluster1]
pacemaker-[1:3]

[pacemaker:children]
cluster1
```

Notice:
* You should use the hostname as the nodename, eg: "pacemaker-1" should be the hostname of 10.10.10.11
* The variable "role" is used to config cluster and vip. In the group, there is at most one node with variable "role" defined, and its value must be "operator"
* We use the group pacemaker, the group "cluster1" is temporary, you can name it as you want


## Role Variables
No required vars, the optional var as follows:

The cluster member communicates each other with hostname, if your hostname is not register in DNS server, please set **add_ip_hostname_in_etc_hosts** to **true** to add ip and hostname in /etc/hosts

```
add_ip_hostname_in_etc_hosts: true
```

The default password of user hacluster is "Change@Me", you can change it by setting **user_hacluster_password**:

```
user_hacluster_password: ChangE@Me
```

If you want to add vip in the cluster, you should set the following variables:

```
cluster_name: default_cluster
vip_name: "ClusterIP"
vip_address: "10.10.10.10"
vip_cidr: "24"
vip_monitor_interval: "30"
```

If you want to add vip to a specificed interface(for example "eth0"), the can set the **vip_nic_name** variable:

```
vip_nic_name: "eth0"
```


The whole variables of role as follows:

```
add_ip_hostname_in_etc_hosts: true
user_hacluster_password: Change@Me

cluster_name: default_cluster
vip_name: "ClusterIP"
vip_address: "10.10.10.10"
vip_cidr: "24"
vip_monitor_interval: "30"
# vip_nic_name: "eth0"
```


## Example Playbook

```
---
- hosts: pacemaker
  become: true
  roles:
    - /path/to/pacemaker/role
```


## Notice
1. In OpenStack or other cloud environment, the secondary ip on the same interface may not be allowed, but VMWare Exsi supports this feature. This role is tested on the VMWare Exsi and work well.
2. This role do not supply the function to change name of cluster or vip configuration(name, address, nic and so on), you can use the command **pcs cluster destroy --all** to destory cluster and vip resource, then use this role with correct parameter to deploy


## License
MIT

