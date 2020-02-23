# Change size (replication factor)
ceph osd pool set <pool-name> min_size <min-size>
ceph osd pool set <pool-name> size <size>

# Manage pool
### Create pool
ceph osd pool create pool <pool-name> <pg-size>

### Pool detail
ceph osd pool ls detail

### Change size (replication factor)
- ceph osd pool set <pool-name> min_size <min-size>
- ceph osd pool set <pool-name> size <size>

### Check health
- ceph osd pool ls detail

# Mount RBD
### create new pool
- ceph osd pool create cs100 8

### Rename Pool
- ceph osd pool rename <poolname> <newpoolname>

### Remove Pool
- ceph osd pool rm <pool-name> <pool-name> --yes-i-really-really-mean-it


### Initialize Pool
- rbd pool init cs100

### check Pool application
- ceph osd pool ls detail | grep cs100

### Create Image
- rbd create cs100/foo --size 4096 --image-feature layering

## Install and mount rbd (outside container)
### Install ceph client manual
- wget -q -O- 'https://download.ceph.com/keys/release.asc' | sudo apt-key add -
- echo deb https://download.ceph.com/debian-nautilus/ $(lsb_release -sc) main | sudo tee /etc/apt/sources.list.d/ceph.list
- apt update
- apt install ceph-common

### Check ceph config file
- cat /etc/ceph/ceph.conf

### Try to calling ceph mon
- ceph -s

### Map Image 
- rbd map cs100/foo

### Make Filesystem
- mkfs.ext4 /dev/rbd/cs100/foo

### Mount rbd
- mkdir /mnt/cs100-foo
- mount /dev/rbd/cs100/foo /mnt/cs100-foo
- cd /mnt/cs100-foo/
- echo "Test write text on file" > ./file.txt
- ls -la
