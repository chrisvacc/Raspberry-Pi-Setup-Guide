# Rockchip Setup Guide

Installing Arch Linux ARM on the [Iconikal Rockchip RK3328](https://www.amazon.com/Iconikal-Rockchip-Computer-Processor-1866MHz/dp/B0868WSTXH) Single Board Computer

#### 1.1.2 MacOS

Run diskutil and find your SD card - It will be listed as (external, physical) and approximate your SD card's size

`diskutil list`

Zero the beginning of the SD card:

`sudo dd if=/dev/zero of=/dev/disk2 bs=1000000 count=32`

Start gdisk to partition the SD card (Replace X with your SD card location.):

`sudo gdisk /dev/disk2`

At the gdisk prompt, create the new partition:

Type `o`. This will clear out any partitions on the drive. 

Type `p` to list partitions. There should be no partitions left.

Type `n`, then `1` for the first partition on the drive, `32768` for the first sector, and then press `ENTER` to accept the **default** last sector.

Hex code or GUID: `8300`

Write the partition table and exit by typing `w`.

Create a directory to boot from 
`mkdir boot`

Unmount disk (to avoid "Resource busy" error)
`diskutil unmountDisk disk2`

Create the ext4 filesystem:
`sudo /usr/local/opt/e2fsprogs/sbin/mkfs.ext4 /dev/disk2s1`

Mount the filesystem:

`mkdir root`

`sudo mount -t ufsd_ExtFS /dev/disk2s1 root`

Download and extract the root filesystem (as root, not via sudo):

`sudo su`

`wget http://os.archlinuxarm.org/os/ArchLinuxARM-aarch64-latest.tar.gz`

`tar -xpf ArchLinuxARM-aarch64-latest.tar.gz -C /Users/[your-name]/root`

Download the boot.scr script for U-Boot and place it in the /boot directory:

`wget http://os.archlinuxarm.org/os/rockchip/boot/rock64/boot.scr -O root/boot/boot.scr`

Unmount the partition:

`umount root`

Download and install the U-Boot bootloader:

`wget http://os.archlinuxarm.org/os/rockchip/boot/rock64/rksd_loader.img`

`wget http://os.archlinuxarm.org/os/rockchip/boot/rock64/u-boot.itb`

`dd if=rksd_loader.img of=/dev/sdX seek=64 conv=notrunc`

`dd if=u-boot.itb of=/dev/sdX seek=16384 conv=notrunc`

Insert the micro SD card into the Rock64, connect ethernet, and apply 5V power.
Use the serial console or SSH to the IP address given to the board by your router.
Login as the default user alarm with the password alarm.
The default *root password* is **root**.
HDMI video is not yet supported in the mainline kernel.
Initialize the pacman keyring and populate the Arch Linux ARM package signing keys:

`pacman-key --init`
`pacman-key --populate archlinuxarm`

Install the U-Boot package

Remove the boot.scr file manually downloaded previously:

`rm /boot/boot.scr`

Install the U-Boot package:

`pacman -Sy uboot-rock64`

When prompted, press `y` and hit `ENTER` to write the latest bootloader to the micro SD card.

Remove the Arch Linux TarBall from the home directory

`mv ~/ArchLinuxARM-aarch64-latest.tar.gz ~/Downloads`
or
`rm ~/ArchLinuxARM-aarch64-latest.tar.gz`

Copyright ©2009-2020 Arch Linux ARM
The registered trademark Linux® is used pursuant to a sublicense from LMI, the exclusive licensee of Linus Torvalds, owner of the mark on a world-wide basis.
The Arch Linux™ name and logo are used under permission of the Arch Linux Project Lead.
