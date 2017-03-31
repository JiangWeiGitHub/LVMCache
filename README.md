# Using LVM’s new cache feature

### Reference
[Link Site](https://rwmj.wordpress.com/2014/05/22/using-lvms-new-cache-feature/)


### Caveat
+ `lvm` version is important

  Mine is `LVM TOOLS 2.02.133(2) (2015-10-30)`, or the command `lvconvert --type cache-pool ...` would fail, just like `WARNING: Unrecognised segment type cache-pool`


+ put small capacity logic volume in front of big one when using `lvconvert`

  `lvconvert` command can create `cache pool` or enable `cache`, your input should like this `lvconvert --type cache-pool --poolmetadata lv-small-one lv-big-one`


### Installation
+ `apt install lvm2`

### Development Enviroment
+ Windows 7 Ultimate 64bit
+ VMware Workstation 12 Pro
  - CPU: 1 CPU 4 Core
  - Memory: 2G
  - Network: Bridge
  - Hard disk: 80G -> System disk (Ubuntu 16.04.1 amd64 Desktop)
  - Hard disk: 10G -> Cache disk (/dev/sdb) -> 1G for meta cache, 5G for cache
  - Hard disk: 20G -> Storage disk (/dev/sdc)
  - Others: Default
  
### Procedure
```
  pvcreate /dev/sdb
  pvcreate /dev/sdc
  vgcreate vghardandssd /dev/sdc
  lvcreate -L 10g -n lvhard vghardandssd
  lvdisplay 
  mkfs -t ext4 /dev/vghardandssd/lvhard
  vgextend vghardandssd /dev/sdb
  lvcreate -L 1G -n lvssdmeta vghardandssd /dev/sdb
  lvcreate -L 5G -n lvssd vghardandssd /dev/sdb
  lvs
  pvs
  lvconvert --type cache-pool --poolmetadata vghardandssd/lvssdmeta vghardandssd/lvssd
  lvconvert --type cache --cachepool  vghardandssd/lvssd vghardandssd/lvhard
```
