panda
=============

A python library for deploying and testing VIO.


Installation
=============

 python setup.py install

 pip install --index-url http://p3-pypi.eng.vmware.com:3141/slave/dev/+simple --trusted-host p3-pypi.eng.vmware.com panda


Usage
======
System libs are required by tempest:
python-dev, libffi-dev, libssl-dev, git, swig, libxml2-dev, libxslt-dev. Install them by apt-get install.

python virtualenv is required by 'panda tempest' command, install them beforehand.

Get help:
 panda [command] [sub command] -h

Deploy VIO vApp:
 panda oms deploy '192.168.111.130' 'Administrator@vsphere.local' 'Admin!23' 'vio-datacenter' 'mgmt_cluster' 'vdnetSharedStorage' 'VM Network' 'http://build-squid.eng.vmware.com/build/mts/release/bora-3302254/publish/openstack_vapp/exports/ova/VMware-OpenStack-2.0.0.0-3302254_OVF10.ova' --ip '192.168.111.151' --netmask '255.255.255.0' --dns '192.168.111.1' --password 'vmware' --gateway '192.168.111.1'

Config omjs.properties:
 panda oms config-omjs '192.168.111.151' 'vmware' 'Administrator@vsphere.local' 'Admin!23' 'oms.use_linked_clone=true,oms.skip_cluster_vmotion_check=true,oms.disable_datastores_anti_affinity=true'

Remove VIO vApp:
 panda oms remove '192.168.111.130' 'Administrator@vsphere.local' 'Admin!23' '^VMware-OpenStack.*\d$'

Create Openstack cluster:
 panda cluster create '192.168.111.151' 'Administrator@vsphere.local' 'Admin!23' cluster_spec.json

Delete Openstack cluster:
 panda cluster delete "192.168.111.151" 'Administrator@vsphere.local' 'Admin!23'

Enable LDAP backend admin user on multiple backend setup.
 panda openstack enable_ldap_admin '192.168.111.160' 'admin' 'vmware' 'vioadmin@vio.com'

Run tempest:
 Step 1
  Install tempest:
   panda tempest install

 Step 2
  Configure tempest against a SQL keystone/NSXv neutron backend VIO:
   panda tempest config '192.168.111.160' 'admin' 'vmware' 'nsxv' --ext-cidr '192.168.112.0/24' --ext-start-ip '192.168.112.170' --ext-end-ip '192.168.112.200' --ext-gateway '192.168.112.1' --compute-nodes 2 --nsx-manager '192.168.111.15' --nsx-user 'admin' --nsx-password 'default'
  Configure tempest against a SQL keystone/DVS neutron backend VIO:
   panda tempest config '192.168.111.153' 'admin' 'vmware' 'dvs' --compute-nodes 2
  Configure tempest against a LDAP keystone backend VIO:
   panda tempest config '192.168.111.160' 'vioadmin@vio.com' 'VMware1!' 'nsxv' --credentials-provider 'pre-provisioned' --user1 'xiaoy@vio.com' --user1-password 'VMware1!' --user2 'sren@vio.com' --user2-password 'VMware1!' --ext-cidr '192.168.112.0/24' --ext-start-ip '192.168.112.170' --ext-end-ip '192.168.112.200' --ext-gateway '192.168.112.1' --nsx-manager '192.168.111.15' --nsx-user 'admin' --nsx-password 'default'

 Step 3
  Run tempest tests:
   panda tempest run 'keystone,glance,nova,cinder,neutron,heat,scenario,nsxv,nsxt'

Run VMware tempest:
 Step 1
  Install VMware tempest:
   panda vmware_tempest install

 Step 2
  Configure VMware tempest
   panda vmware_tempest config '192.168.111.130' 'Administrator@vsphere.local' 'Admin!23'

 Step 3
  Run VMware tempest tests:
   panda vmware_tempest run

All in one command:
 panda go oms_spec.json cluster_spec.json --tests 'keystone,glance,nova,cinder,neutron,heat,scenario,vmware'
