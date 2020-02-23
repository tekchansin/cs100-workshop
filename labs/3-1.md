## Benchmarking
### Clear filesystem cache before run
- echo 3 | sudo tee /proc/sys/vm/drop_caches && sudo sync
### Create pool for benchmarking
- ceph osd pool create testbench 100 100
### A write test for 10 seconds
- rados bench -p testbench 10 write --no-cleanup
### A sequential read test for 10 seconds
- rados bench -p testbench 10 seq
### A random read test for 10 seconds
- rados bench -p testbench 10 rand
### Custom bench
- rados bench -p testbench 10 write -t 4 --run-name client1
- t = threads(16)
- b = size of object (4M)
### Write performance test against the block device
- rbd create testbench/image01 --size 1024
- rbd bench-write image01 --pool=testbench



