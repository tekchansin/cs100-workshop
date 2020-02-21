### Specification
- ubuntu 18.04
- ceph-ansible tag: v4.0.15

# Installation
### Checkout code
```
git clone https://github.com/tekchansin/cs100-workshop.git /opt/cs100
cd /opt/cs100
git submodule update --init
```
### Install dependency
```
apt update
apt install python-pip virtualenv -y
```

### Setup Environment:
```
virtualenv /opt/cephenv --system-site-packages
source /opt/cephenv/bin/activate
pip install -r /opt/cs100/ceph-ansible/requirements.txt
```

### Clean device before join cluster
#### Zap device specific node
- ssh to node
```
wipefs -a /dev/sdb /dev/sdc /dev/sdd /dev/sde /dev/sdf /dev/sdh /dev/sdi
```
### Generate key on deploy node
```
ssh-keygen
```

### copy public key to other node
```
cat ~/.ssh/id_rsa.pub > /home/nc-user/.ssh/authorized_keys
```
### Configure Ansible
```
mkdir /etc/ansible
cat > /etc/ansible/ansible.cfg <<EOF
[defaults]
host_key_checking=False
pipelining=True
forks=100
EOF
```

## Deploy Ceph-ansible
```
cd /opt/cs100/ceph-ansible
ansible-playbook -b -i ../hosts.ini site-docker.yml.sample
```

# Configuration
## Enable NFS ganesha in dashboard
- docker exec -it ceph-mgr-cephserver-1 bash
- ceph dashboard set-ganesha-clusters-rados-pool-namespace cephfs_data

## Create ErasureCode pool

#### get profile
- ceph osd erasure-code-profile get default

#### create profile
- ceph osd erasure-code-profile set ecprofile k=2 m=1 crush-failure-domain=host

#### create pool
- ceph osd pool create ecpool 128 128 erasure ecprofile


## Create Cache tier 
(https://docs.ceph.com/docs/nautilus/rados/operations/cache-tiering/)
#### Map cache pool to data pool (1 to 1 mapping)
- ceph osd pool create {cachepool} 128 128
- ceph osd tier add {storagepool} {cachepool}
- ceph osd tier cache-mode {cachepool} writeback
- ceph osd pool set {cachepool} hit_set_type bloom

#### Cache settings
##### A larger hit_set_count results in more RAM consumed by the ceph-osd process.
- ceph osd pool set {cachepool} cache_target_dirty_ratio 0.4
- ceph osd pool set {cachepool} cache_target_dirty_high_ratio 0.6
- ceph osd pool set {cachepool} cache_target_full_ratio 0.8
- ceph osd pool set {cachepool} cache_min_flush_age 600
- ceph osd pool set {cachepool} cache_min_evict_age 1800

## Remove cache
#### Set readonly cache & flush write
- ceph osd tier cache-mode {cachepool} proxy
- wait until cache write file flush
- rados -p {cachepool} ls

#### Manual flush (optional)
- rados -p {cachepool} cache-flush-evict-all
- ceph osd tier cache-mode {cachepool} none
- ceph osd tier remove {storagepool} {cachepool}

# Benchmark
#### Crypto Benchmark
- yum install cryptsetup
- cryptsetup benchmark
#### RBD bench
- rbd -p ecpool_cache bench test --io-type write --io-size 8192 --io-threads 256 --io-total 10G --io-pattern seq

# Management Module

## Alert (send alert to email)
https://docs.ceph.com/docs/master/mgr/alerts/

## Disk prediction
https://docs.ceph.com/docs/master/mgr/diskprediction/

## Zabbix
https://docs.ceph.com/docs/master/mgr/zabbix/
#### Config
```
ceph zabbix config-set zabbix_server_ip
ceph zabbix config-set agent_hostname
ceph zabbix config-show
```
#### Test send data
```
ceph zabbix send
ceph zabbix discovery
```

#### Zabbix template
https://raw.githubusercontent.com/ceph/ceph/9c54334b615362e0a60442c2f41849ed630598ab/src/pybind/mgr/zabbix/zabbix_template.xml
