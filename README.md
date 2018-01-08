# Installation of a DC/OS cluster over Centos7 

Assuming the dcox-pxe guest is up and running

## Now you can create other guests 
### Create a boot guest

```
virsh destroy dcos-boot;virsh undefine dcos-boot;rm -rf /opt/dcos/guests/dcos-boot/
```

```
mkdir -p /opt/dcos/guests/dcos-boot/

virt-install \
 -n dcos-boot \
 --description="DCOS BOOT machine" \
 --os-type=Linux \
 --os-variant=generic \
 --ram=1200 \
 --vcpus=1 \
 --graphics=vnc \
 --noautoconsole \
 --disk path=/opt/dcos/guests/dcos-boot/dcos-boot.img,bus=virtio,size=10 \
 --pxe \
 --boot=hd,network \ 
 --network=bridge:dcos-br0,model=virtio,mac=52:54:00:e2:87:5c
```
 
### Create a master guest
```
virsh destroy dcos-master1;virsh undefine dcos-master1;rm -rf /opt/dcos/guests/dcos-master1/
```

```
mkdir -p /opt/dcos/guests/dcos-master1/

virt-install \
 -n dcos-master1 \
 --description="DCOS Master 1 machine" \
 --os-type=Linux \
 --os-variant=generic \
 --ram=4500 \
 --vcpus=1 \
 --graphics=vnc \
 --noautoconsole \
 --disk path=/opt/dcos/guests/dcos-master1/dcos-master1.img,bus=virtio,size=7 \
 --pxe \
 --boot=hd,network \ 
 --network=bridge:dcos-br0,model=virtio,mac=52:54:00:e2:87:5d
```
 
### Create a agent guest
```
virsh destroy dcos-agent1;virsh undefine dcos-agent1;rm -rf /opt/dcos/guests/dcos-agent1/
```
 
```
mkdir -p /opt/dcos/guests/dcos-agent1/

virt-install \
 -n dcos-agent1 \
 --description="DCOS Agent 1 machine" \
 --os-type=Linux \
 --os-variant=generic \
 --ram=7000 \
 --vcpus=2 \
 --graphics=vnc \
 --noautoconsole \
 --disk path=/opt/dcos/guests/dcos-agent1/dcos-agent1.img,bus=virtio,size=7 \
 --pxe \
 --boot=hd,network \ 
 --network=bridge:dcos-br0,model=virtio,mac=52:54:00:e2:87:5e
```

```
virsh destroy dcos-agent2;virsh undefine dcos-agent2;rm -rf /opt/dcos/guests/dcos-agent2/
```

```
mkdir -p /opt/dcos/guests/dcos-agent2/

virt-install \
 -n dcos-agent2 \
 --description="DCOS Agent 2 machine" \
 --os-type=Linux \
 --os-variant=generic \
 --ram=7000 \
 --vcpus=2 \
 --graphics=vnc \
 --noautoconsole \
 --disk path=/opt/dcos/guests/dcos-agent2/dcos-agent2.img,bus=virtio,size=7 \
 --pxe \
 --boot=hd,network \ 
 --network=bridge:dcos-br0,model=virtio,mac=52:54:00:e2:87:65
```
 
### Create a NFS guest
```
virsh destroy dcos-nfs1;virsh undefine dcos-nfs1;rm -rf /opt/dcos/guests/dcos-nfs1/
```
 
```
mkdir -p /opt/dcos/guests/dcos-nfs1/

virt-install \
 -n dcos-nfs1 \
 --description="DCOS NFS 1 machine" \
 --os-type=Linux \
 --os-variant=generic \
 --ram=1500 \
 --vcpus=1 \
 --graphics=vnc \
 --noautoconsole \
 --disk path=/opt/dcos/guests/dcos-nfs1/dcos-nfs1.img,bus=virtio,size=7 \
 --pxe \
 --boot=hd,network \ 
 --network=bridge:dcos-br0,model=virtio,mac=52:54:00:e2:87:5f
```

If this is a reinstallation the guests should be removed from the known_hosts
```
ssh-keygen -R 192.168.40.20 -f /home/fernando.hackbart/.ssh/known_hosts
ssh-keygen -R 192.168.40.30 -f /home/fernando.hackbart/.ssh/known_hosts
ssh-keygen -R 192.168.40.40 -f /home/fernando.hackbart/.ssh/known_hosts
ssh-keygen -R 192.168.40.47 -f /home/fernando.hackbart/.ssh/known_hosts
ssh root@192.168.40.20 date
ssh root@192.168.40.30 date
ssh root@192.168.40.40 date
ssh root@192.168.40.47 date
```

After the guests are created the Ansible playbook can be run against the guests
```
git clone https://github.com/fernandohackbart/ansible-dcos-centos.git
cd ansible-dcos-centos
```

Check the hosts file
```
cat hosts
```

Run the playbook
```
ansible-playbook -i hosts dcos.yml
```


After running the playbook we can connect to the DC/OS console
```
http://192.168.40.30
```
