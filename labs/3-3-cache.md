## Cache
### Create a replicated cache pool
- ceph osd pool create {cachepool} {pg}

### Apply cache to data pool
- ceph osd tier add {datapool} {cachepool}
- ceph osd tier cache-mode {cachepool} writeback

## Remove cache
### Set readonly cache & flush write
- ceph osd tier cache-mode {cachepool} proxy
### wait until cache write file flush
- rados -p {cachepool} ls
### Manual flush (optional)
- rados -p {cachepool} cache-flush-evict-all
### Remove cache tier
- ceph osd tier cache-mode {cachepool} none
- ceph osd tier remove {datapool} {cachepool}