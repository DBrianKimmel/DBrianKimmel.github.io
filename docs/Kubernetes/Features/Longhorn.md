# Longhorn

There is an issue with Raspberry Pi / Ubuntu: the names for disks are assigned almost at random.
 Therefore, even if you have USB disks in the same slots on multiple nodes, they can be named at random, /dev/sda or /dev/sdb, and there is no easy way to enforce the naming, without messing with udev rules a lot.

## Identifying disks for storage

We are going to use Ansible again a lot, and will add new variables with disk names that will be used for storage into /etc/ansible/hosts.

We are going to use the lsblk -f command on every node and look for disk labels:

``` bash
ansible cube -b -m shell -a "lsblk -f"
```

Results:

``` bash
pi-12-pp | CHANGED | rc=0 >>
NAME                      FSTYPE      LABEL        UUID                                   FSAVAIL FSUSE% MOUNTPOINT
sda                                                                                                      
├─sda1                    vfat                     302E-C0DF                                             
├─sda2                    ext4        ssd-04-arm32 9006c7c6-4791-43f1-bc65-5262c472f64b                  
├─sda3                    ext4        ssd-04-arm64 ad3c04f1-4cea-4118-bb71-cd937960c9a9      3.8G    71% /
└─sda4                    LVM2_member              WYao6H-RtuF-J6e3-D1VE-UENC-vWZc-tNx0js                
  └─vg--ctl--01-lv--store ext4                     1e3438fb-1ce4-4c3b-a709-b397708fdc06     18.5G     0% /var/storage
mmcblk0                                                                                                  
├─mmcblk0p1               vfat        system-boot  4D3B-86C0                               135.9M    46% /boot/firmware
└─mmcblk0p2               ext4        writable     79af43d1-801b-4c28-81d5-724c930bcc83       11G    19% /SD

pi-17-pp | CHANGED | rc=0 >>
NAME                     FSTYPE      LABEL        UUID                                   FSAVAIL FSUSE% MOUNTPOINT
sda                                                                                                     
├─sda1                   vfat                     49CF-1F5A                                             
├─sda2                   ext4        ssd-07-arm32 461215ce-ea53-4996-8697-54420e7a1da3                  
├─sda3                   ext4        ssd-07-arm64 b0ac490f-5f83-4ed1-9b94-8a52436b955f     10.3G    29% /
└─sda4                   LVM2_member              fppIPT-cI0k-Ki17-3ajV-j40Z-toBg-ICwV69                
  └─vg--ctl--02-lv_store ext4                     fd92218f-9c8a-4e9c-bb06-8d3704bd9b0a     18.5G     0% /var/storage
mmcblk0                                                                                                 
├─mmcblk0p1              vfat        system-boot  4D3B-86C0                               132.8M    47% /boot/firmware
└─mmcblk0p2              ext4        writable     79af43d1-801b-4c28-81d5-724c930bcc83       10G    26% /SD

pi-15-pp | CHANGED | rc=0 >>
NAME                    FSTYPE      LABEL        UUID                                   FSAVAIL FSUSE% MOUNTPOINT
sda                                                                                                    
├─sda1                  exfat                    7C8B-3E4C                                             
├─sda2                  ext4        ssd-05-arm32 f0ac0c45-7ab2-4f11-8373-7a2f568a3bc2                  
├─sda3                  ext4        ssd-05-arm64 91f02013-d751-4182-8bcb-5bf6f54498b7     10.4G    28% /
└─sda4                  LVM2_member              4pM34i-8p3I-6BNB-LgX3-rc0Q-ICh1-tz5Mzs                
  └─vg_wrk--01-lv_store ext4                     69a82734-94e5-4c03-b5d0-99aa5cfcc794     18.5G     0% /var/storage
mmcblk0                                                                                                
├─mmcblk0p1             vfat        system-boot  4D3B-86C0                               132.8M    47% /boot/firmware
└─mmcblk0p2             ext4        writable     79af43d1-801b-4c28-81d5-724c930bcc83       23G    16% /SD

pi-16-pp | CHANGED | rc=0 >>
NAME                    FSTYPE      LABEL        UUID                                   FSAVAIL FSUSE% MOUNTPOINT
sda                                                                                                    
├─sda1                  exfat                    18D0-56F4                                             
├─sda2                  ext4        ssd-06-arm32 352ed1b1-4a1e-4cc5-b0e3-c9c3cb34215d                  
├─sda3                  ext4        ssd-06-arm64 33ad9dc7-82c8-4f49-9521-b0590044d68c     10.6G    27% /
└─sda4                  LVM2_member              X7LxNz-0G1w-ZAAi-uiXQ-WwWK-ZEvR-r6R16z                
  └─vg--wrk--02-lv_stor ext4                     23ab704c-0fb8-4d38-824c-8168ca1d8602     18.5G     0% /var/storage
mmcblk0                                                                                                
├─mmcblk0p1             vfat        system-boot  4D3B-86C0                               132.8M    47% /boot/firmware
└─mmcblk0p2             ext4        writable     79af43d1-801b-4c28-81d5-724c930bcc83     10.9G    19% /SD

pi-18-pp | CHANGED | rc=0 >>
NAME                  FSTYPE      LABEL        UUID                                   FSAVAIL FSUSE% MOUNTPOINT
sda                                                                                                  
├─sda1                                                                                               
├─sda2                ext4        ssd-08-arm32 b7a16d06-0811-43d3-9326-e1b0f0a00b61                  
├─sda3                ext4        ssd-08-arm64 e8ab8e92-a75b-4905-8660-d7a88075514d     11.4G    22% /
└─sda4                LVM2_member              UJzcYL-4740-IGbL-szaG-T7xh-WA0C-I3cgA6                
  └─vg_ctl_03-lv_stor ext4                     04383936-778a-4b6c-bb8c-850ebfe042a4     18.5G     0% /var/storage
mmcblk0                                                                                              
├─mmcblk0p1           vfat        system-boot  4D3B-86C0                               132.8M    47% /boot/firmware
└─mmcblk0p2           ext4        writable     79af43d1-801b-4c28-81d5-724c930bcc83     10.6G    22% /SD
```

Edit /etc/ansible/hosts, and add a new variable (I have chosen name var_disk) with the disk to wipe.
TAKE YOUR TIME AND LOOK TWICE ! the wipefs command we’re gonna use will not wipe your OS disk but it might wipe any other that is not mounted.

[control]
control01  ansible_connection=local var_hostname=control01 var_disk=sda
control02  ansible_connection=ssh   var_hostname=control02 var_disk=sdb
control03  ansible_connection=ssh   var_hostname=control03 var_disk=sdb

[workers]
cube01  ansible_connection=ssh  var_hostname=cube01 var_disk=sdb
cube02  ansible_connection=ssh  var_hostname=cube02 var_disk=sdb
cube03  ansible_connection=ssh  var_hostname=cube03 var_disk=sdb
cube04  ansible_connection=ssh  var_hostname=cube04 var_disk=sdb
cube05  ansible_connection=ssh  var_hostname=cube05 var_disk=sdb
cube06  ansible_connection=ssh  var_hostname=cube06 var_disk=sda

[cube:children]
control
workers

