# Linux Install on RAID Configuration

* prerequisites:

1. partition the disks

    * note: you may type print at any time to see what you created
    * it is a good practice to leave 100MB unallocated space at end of disk so you can repair the raid with the same size disk easier

    ```bash
    $ parted /dev/sda
    (parted) unit mb
    (parted) mklabel gpt
    (parted) mkpart primary 1 125
    (parted) name 1 boot
    (parted) mkpart primary 125 -100
    (parted) name 2 rootfs
    (parted) quit
    ```

    * repeat for other disks

2. create array

    * for boot partition, we'll use raid 1

    ```bash
    mdadm --create /dev/md0 --level=1 --raid-disks=3 /dev/sda1 /dev/sdb1 /dev/sdc1
    ```

    * for the system parition, we'll use raid 5
  
    ```bash
    mdadm --create /dev/md1 --level=5 --raid-disks=3 /dev/sda2 /dev/sdb2 /dev/sdc2
    ```

    * to see your arrays,

    ```bash
    cat /proc/mdstat
    ```

3. format the arrays

    * since we want to have legacy boot, both raid arrays can be ext4, you can experiment with other filesystems, but I'll be using ext4
  
    ```bash
    mkfs.ext4 /dev/md0
    mkfs.ext4 /dev/md1
    ```

    * at this point, you can now install your linux system using md2 as your root partition, mount md0 as your boot partition
