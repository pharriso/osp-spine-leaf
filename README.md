# osp-spine-leaf

Configures per rack tenant & storage networks for use with spine+leaf & RHEL OSP Director.

To use the templates (as stack user):

mkdir templates
cp -r /usr/share/openstack-tripleo-heat-templates/* templates

Overlay the template files in this repo over the copied templates directory.

Create new flavors for the number of racks required:

openstack flavor create --vcpus 1 --ram 4096 --disk 40 computeRack1
openstack flavor set --property capabilities:boot_option='local' --property capabilities:profile='computeRack1' computeRack1

Tag ironic nodes:

ironic node-update compute-1 add properties/capabilities=profile:computeRack1,boot_option:local

Update ~stack/templates/environments/storage-environment.yaml, ~stack/templates/environments/network-environment.yaml to suit your environment & ~stack/templates/environments/overcloud-environment.yaml

Deploy:

openstack overcloud deploy --templates /home/stack/templates/ --ntp-server 192.168.10.1 -e ~stack/templates/environments/network-environment.yaml -e ~stack/templates/environments/storage-environment.yaml ~stack/templates/environments/overcloud-environment.yaml --control-scale 1 --compute-scale 0 --control-flavor control --compute-flavor compute
