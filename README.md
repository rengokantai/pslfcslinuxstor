#### pslfcslinuxstor
#####partitioning
######fdisk
basic
```
lsblk
fdisk -l /dev/xvdf
```
fdisk
```
fdisk /dev/xvdf
n->p->enter->enter->+20M
```
change partition type: t HExcode82=>swap partition, L=list all type w=write d=delete
```
dd if=/dev/zero of=/dev/xvdf1 count=1 bs=512
```
######gdisk
```
gdisk /dev/xvdf
```
?=help, then create 2 partition:
```
n->enter->enter->+20M->8300
n->enter->enter->+20M->8200
w->Y
```
wipe out GPT header
```
dd if=/dev/zero of=/dev/xvdf count=2 bs=16K
```
######parted
print info
```
parted /dev/xvdf print
```
begin
```
parted
select /dev/xvda
mklabel msdos
mklabel gpt(destroy table)
p(print)
mkpart primary 1 200 (mega)
mkpart extended 201 300  (or 201 -1)
mkpart logical 202 300
quit
```
destroy:
```
dd if=/dev/zero of=/dev/xvdf1 count=1 bs=512
```
using parted with script like:
```
#!/bin/bash
disk="dev/xvdf"
parted -s $disk -- mklabel msdos mkpart extended 1m -1m
parted -s $disk mkpart logical linux-swap 2m 100m
parted -s $disk mkpart logical 101m 200m 
...
parted -s $disk set 10 lvm on
parted -s $disk set 13 raid on
parted -s $disk print
```
