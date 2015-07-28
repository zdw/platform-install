# opencloud-cluster-setup
This repository contains [Ansible](http://docs.ansible.com) playbooks for installing and configuring an OpenStack cluster 
for use in [OpenCloud](http://guide.opencloud.us).  All of the OpenStack controller services are installed in VMs on a 
single "head node" and connected by an isolated private network. [Juju](http://www.ubuntu.com/cloud/tools/juju) is used 
to install and configure the OpenStack services.

## How to use it

The playbook is designed to be run on a separate control machine that has a recent version of Ansible installed (e.g., a laptop).
Here are the basic steps for installing a new OpenCloud cluster named 'foo':

* Install Ubuntu 14.04 LTS on the head and compute nodes.
* Create *foo-setup.yaml* and *foo-compute.yaml* files using *singapore-setup.yaml* and *singapore-compute.yaml* as templates.  
  * Change the *- hosts:* lines as appropriate for your own head and compute nodes.  
  * Change *opencloud3.sing.internet2.edu* to the DNS name of your head node.
* On the control machine, run: *$ ansible-playbook foo-setup.yaml*.  This will set up Juju, use it to install the OpenStack services on the head node, and prep the compute nodes.
* Log into the head node.  For each compute node, put it under control of Juju, e.g.: *$ juju add-machine ssh:ubuntu@compute-1*.
* On the control machine, run: *$ ansible-playbook foo-compute.yaml* to install the nova-compute service on the compute nodes that were added to Juju.

## Things to note

* The installation configures port forwarding so that the OpenStack services can be accessed from outside the private network. Some OpenCloud-specific firewalling is also introduced, which will likely require modification for other setups.  See: [files/etc/libvirt/hooks/qemu](https://github.com/andybavier/opencloud-cluster-setup/blob/master/files/etc/libvirt/hooks/qemu).
* By default the compute nodes are controlled and updated automatically using *ansible-pull* from [this repo](https://github.com/andybavier/opencloud-nova-compute-ansible).  You may want to change this.
* All of the service interfaces are configured to use SSL because that's what OpenCloud uses in production.  Again, may not be what you want.  Look for the relevant Juju commands in *singapore-setup.yaml*.
