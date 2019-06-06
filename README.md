OKD - 3.11
==========

This **Vagrantfile** create 4 machines, one with the roles "master" and "infra", other two with the "node" role and another one for storage purposes:

| Machine        | Address      | Roles         |
|----------------|--------------|---------------|
| master.okd.os  | 27.11.90.10  | master, infra |
| node1.okd.os   | 27.11.90.20  | node          |
| node2.okd.os   | 27.11.90.30  | node          |
| storage.okd.os | 27.11.90.40  | -             |

Everything is installed during the provisioning stage, this means that after the provisioning step, vagrant execute these two commands:

    ansible-playbook /root/openshift-ansible/playbooks/prerequisites.yml
    ansible-playbook /root/openshift-ansible/playbooks/deploy_cluster.yml

The ansible on master machine is preconfigured with the keys and the hosts to access the other machines without problems.
During provisioning step, the preconfigured **inventory** presented in *files/hosts* is copied to the master's */etc/ansible/hosts*.

NAT
---

Some configurations on the inventory */etc/ansible/hosts* was added to overcome problems with default NAT interface that vagrant creates:

 - etcd_ip
 - openshift_public_ip
 - openshift_public_hostname

Disabled Services
-----------------

 - openshift_logging_install_logging
 - openshift_enable_olm
 - ansible_service_broker_install
 - template_service_broker_install

Requirements
------------

From a software point of view, you will need a least **VirtualBox**.
From a hardware point of view, each machine uses 2 cpu cores, unless the storage one. The master is configured to use 6GiB of RAM, the node ones 2GiB and the storage 256MiB, so you will need at least 11GiB of RAM, or even less if you lower the memory of each vm.
If you disable the **openshift_metrics_install_metrics** you can lower the memory from 6GiB to 2GiB on master, and each node to 1GiB.

Low RAM
-------

You can use the brach named **low-end** and run this lab with 2 machines and 8GiB of RAM.

Installation
------------

This takes a lot of time, just go to the cloned folder and type:

    vagrant up

If you want to access the **webconsole** and/or see metrics you can add the hostnames and ip address on **/etc/hosts**:

    echo '27.11.90.10 master.okd.os' | sudo tee -a /etc/hosts
	echo '27.11.90.10 hawkular-metrics.router.default.svc.cluster.local' | sudo tee -a /etc/hosts

And then access the address [https://master.okd.os:8443](https://master.okd.os:8443).
The **username** and **password** are created in the first login attempt from the username you choose.
