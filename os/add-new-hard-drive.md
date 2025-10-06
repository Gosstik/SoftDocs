# Expand sda partition

### VMWare

Disk can be pre-allocated and sparsed. First one can only be expanded (VM -> Settings -> Hard Drive -> Expand), but it allows system to works faster, so **prefer** to use it for new VMs.

If you want to reduce the size of pre-allocated disk, you can convert it to sparsed and then back to pre-allocated with choosed capacity. **NOTE:** before convertion to sparsed, you have to change partition on guest (ubuntu) OS.

Instruction, how to convert pre-allocated disk to sparsed: [link](https://www.howtogeek.com/313125/how-to-convert-between-preallocated-and-growable-disks-in-vmware/#vmware-workstation).

[forum.cloudron.io](https://forum.cloudron.io/topic/6086/ubuntu-20-04-how-to-extend-partition-for-noobs)

Quick guide:

* Option 1: use `gparted` **with UI** as in this instruction: [youtube](https://www.youtube.com/watch?v=kTEsbS1FbUU). Installation:

```bash
sudo apt-get install gparted -y
```

* Option 2: manual expansion:

```bash
sudo fdisk /dev/sda

#Command (m for help): p
#Command (m for help): d
#Partition number (1-3, default 3): 3 # In this case, only the record about the partition is deleted, the data itself remains on the disk!

#Command (m for help): n
#Partition number (3-128, default 3):3

#First sector (1054720-62914526, default 1054720): 1054720
#Last sector, +/-sectors or +/-size{K,M,G,T,P} (1054720-62914526, default 62914526): 62914526

### Refuse to delete the current filesystem type
#Partition #3 contains a ext4 signature.
#Do you want to remove the signature? [Y]es/[N]o: N

### Save partion table
#Command (m for help): w

### Reboot the virtual machine
reboot

sudo resize2fs /dev/sda3

### Check result
# df -h
```

### How to add sdb

Handy commands:

```bash
sudo lsblk
df -hT  # info about mount points
sudo fdisk -l  # create partition
sudo parted -l  # similar to `fdisk` util
cat /etc/fstab  # permanent mount
sudo blkid  # UUID for permanent mount
```

1) There is a device `/dev/sdb`. Firstly, we have to partition it (use `fdisk`): [https://phoenixnap.com](https://phoenixnap.com/kb/linux-create-partition)

2) Run:

    ```bash
    sudo mkdir /mnt/sdb1
    ```

3) Create a file system on sdb1 (it will generate `UUID`):

    ```bash
    sudo mkfs.ext4 /dev/sdb1
    ```

4) Get `UUID` by running:

    ```bash
    sudo lsblk /dev/sdb1
    ```

5) Add information to `/etc/fstab`:

    ```text
    # sdb1 (win100gb)
    # UUID=6d397792-5c1a-404e-b2eb-8f54d84ab0ff     /home/ownstreamer/win100gb      ext4    uid=ownstreamer,gid=ownstreamer 0       2
    /dev/sdb1     /home/ownstreamer/win100gb      ext4    defaults 0       2
    ```

6) Check that everything works:

    ```bash
    sudo mount -a
    sudo mount /dev/sdb1  # slightly command
    sudo umount /dev/sdb1  # to unmount
    ```

7) Change permissions:

```bash
sudo chown -R ownstreamer:ownstreamer win100gb/
```

### Handy

```bash
sudo apt-get install udisks2 # manage USB and hard drives
```

* mount --bind in fstab: [link](https://serverfault.com/questions/613179/how-do-i-do-mount-bind-in-etc-fstab)

* mount windows disk C (read-only mode): <https://askubuntu.com/questions/145902/unable-to-mount-windows-ntfs-filesystem-due-to-hibernation>. Quick solution:

```bash
sudo mkdir /mnt/Windows/C
sudo mount -t ntfs -o ro /dev/nvme0n1p3 /mnt/Windows/C
```

* /etc/fstab for Windows disks:

```text
#####################################
# Partitions

# Windows disk C
/dev/nvme0n1p3 /mnt/Windows/C/ ntfs ro,defaults 0 2

# Windows disk D
/dev/nvme0n1p4 /mnt/Windows/D ntfs defaults 0 2

# Storage A
/mnt/Windows/D/.VirtualMachines/Ubuntu24.04/StorageA /home/gostik/StorageA none defaults,bind 0 0

# Windows disk E
/dev/nvme0n1p5 /mnt/Windows/E ntfs defaults 0 2
####################################
```

