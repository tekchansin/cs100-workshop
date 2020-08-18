### Deploy ceph with ceph-ansible
- cd ceph-ansible
- ansible-playbook -i ../hosts.ini site.yml

### Check ceph health status
- docker exec -it {mgr-container} bash
- ceph -s
- health status should be "HEALTH_OK"

### OSD status
- ceph osd status

### OSD version
- ceph osd versions

### Pool usage
- ceph df

### OSD raw usage
- ceph osd df
