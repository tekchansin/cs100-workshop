### crush topology
- ceph osd tree

## Device class
### Type of device class
ceph osd crush class ls

### Get device class of osd-id
ceph osd crush get-device-class <osd-id>

### Change class from HDD to SSD
ceph osd crush rm-device-class <osd-id>
ceph osd crush set-device-class ssd <osd-id>
ceph osd crush get-device-class <osd-id>
ceph osd tree

### create bucket 
ceph osd crush add-bucket {bucket-name} {bucket-type}

### move crush location
ceph osd crush move <host-name> root=default rack=a

### remove bucket
ceph osd crush rm <bucket-name>

## Crush Rule
### Create new replicated rule
ceph osd crush rule create-replicated <rule-name> default rack <device-class>

### rule list and detail
ceph osd crush rule ls
ceph osd crush rule dump

### Apply rule to Pool
- ceph osd pool ls detail
- ceph osd pool set <pool-name> crush_rule <rule-name>
- ceph -s
