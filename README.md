ansible-role-lxc-deploy
=====

This role installs and configures [LXC](https://linuxcontainers.org/) and creates LXC containers. 
The containers will be created with Python by default (to be able to use other ansible roles in the containers) and will use static IP addresses (though dnsmasq could be added to have dynamic IPs).
This role is mainly thought to be used with the dependencies to route containers traffic to the Internet through [Tor](https://www.torproject.org/).
Currently, this is achieved by iptables using ferm, but other roles using other iptables managers or directly iptables could be created.
It should be also possible to use other role that configure iptables to masquerade containers traffic to the Internet without using Tor.
This role has only been tested in Debian.

Requirements
------------

This role requires Ansible 1.6 or higher.


Role Variables
--------------

All these variables are defined in vars/main.yml and will be used by default.

    # for lxc-install
    host_user: "user"
    host_user_home: "/home/{{host_user}}"
    lxclib_path: "{{host_user_home}}/lxclib"
    lxccache_path: "{{host_user_home}}/lxccache"

    ## set to true if the host where the role is running is configured to block
    ## all outbound traffic except tor
    usetor: true


    ## for lxc-create-container
    bridge_name: "lxcbr0"
    bridge_network: 172.16.0.0
    bridge_cidr: 24
    bridge_address: 172.16.0.1
    bridge_netmask: 255.255.255.0

    ## by default network is 172.16.0.0/24, if different change 
    ## bridge_address and  bridge_netmask
    container_address: 172.16.0.2
    container_name: "debianwheezy"
    distribution: "wheezy"
    start: true
    ## set to true to add the container ip and name to /etc/hosts
    addtoetchosts: true

The two main variables needed to create new containers (to modify in your 
playbook) are:

    container_address: 172.16.0.2
    container_name: "debianwheezy"

Dependencies
------------

The following roles are recommended:
* ansible-role-tor-basic
* ansible-role-ferm-basic
* ansible-role-ferm-tor
* ansible-role-ferm-route-lxc-through-host-tor

It's not needed to setup any arguments.

Example Playbook
----------

For convenience, there's an ansible project (ansible-prj-lxc-deploy) configured
to use this role and the recommended roles

```
- hosts: localhost

  roles:

    - { role: ansible-role-tor-basic
      }
    - { role: ansible-role-ferm-basic
      }
    - { role: ansible-role-ferm-tor
      }
    - { role: ansible-role-ferm-route-lxc-through-host-tor
      }
    - { role: ansible-role-lxc,
        container_address: "172.16.0.12",
        container_name: "debianwheezyfromansible2",
      }
```

License
-------

GPLv3

Author Information
------------------

Lee Woboo

