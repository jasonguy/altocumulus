# Altocumulus

**IMPORTANT** This is a proof of concept, it might not even work for your Openstack deployment. 
Planning on refactoring this to use the Neutron agent framework as well as Neutron RPC.

## Integrate your Cumulus Linux switch with OpenStack Neutron

This ML2 plugin manages VLAN bridges on the Cumulus Linux switch, facilitating L2 connectivity between Openstack Compute nodes and the traditional linux VLAN bridges. It uses LLDP to perform auto-discovery of compute hosts and the switch ports in which they are connected to.

This uses the same conventions as the Linux Bridge agent so that DHCP/L3 agents can theoretically be hosted on the switch.

## Usage

There are two components involved in this project:
* ML2 mechanism driver (runs on the Openstack Controller hosts with the Neutron server)
* HTTP API server (runs on Cumulus Linux switches)

## Installation
### ML2 mechanism driver
On your Openstack Controller host:
1. Install the driver and its dependencies, with the following commands:

    pip install git+git://github.com/CumulusNetworks/altocumulus.git
    pip install requests

2. Add `cumulus` to the `mechanism_drivers` list in `/etc/neutron/plugins/ml2/ml2_conf.ini`

3. Configure `/etc/neutron/plugins/ml2/ml2_cumulus.ini` with the management address of the Cumulus Linux switch.

### HTTP API server
On the Cumulus Linux switch:
1. Install the API server, with the following commands:

    apt-get install python-pip git
    pip install --upgrade setuptools
    pip install git+git://github.com/CumulusNetworks/altocumulus.git

2. Create the folder `/etc/altocumulus/` and copy the sample `config.yaml` to that folder.
3. Place the included SysVinit script in `/etc/init.d/` on the switches and start the API server with the command:
   `service altocumulus-api start`

## To-do
* Authentication
* Pluggable discovery strategies
* Integration with `oslo.rootwrap` for unprivileged operation
* Working upstart script
* Working SysVinit script
