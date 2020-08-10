# see here for description/context of LUKS/LVM commands
# https://askubuntu.com/a/918030

# Assumption is that this system has 4 hdd, all equal size & speed
# We will use btrfs RAID10 configuration
# The btrfs filesystem is on top of LUKS encryption, one LUKS vol per hdd

# Base system install
#partition tables
gdisk
1 500M, type ef00 (EFI Part)
2 1 Gb type 8300 (Boot Parition)

# Format our EFI partition with FAT32
mkfs.fat -F32 <efi_partition>

# ~!~!~! NOTE ~!~!~!
# If and only if our boot is NOT on btrfs (e.g. usb), create file system on that partition
mkfs.ext2 <boot_partition>

#crypt format
# if using multiple hdd, repeat for each one
cryptsetup -v --cipher aes-xts-plain64 --key-size 512 --hash sha512 --iter-time 2000 --use-urandom --verify-passphrase luksFormat <device>

# crypt open
# Since using multiple hdd, repeat for each one; <luks_mapper> name must be unique per disk
cryptsetup luksOpen <device_a> <luksVolA>
cryptsetup luksOpen <device_b> <luksVolB>
cryptsetup luksOpen <device_c> <luksVolC>
cryptsetup luksOpen <device_d> <luksVolD>

# Create btrfs RAID10, data and metadata spanning all 4 drives
# Use dev mapper LUKS volume names : <luksMapperA> = /dev/mapper/<luksVolA>
mkfs.btrfs -m raid10 -d raid10 <luksMapperA> <luksMapperB> <luksMapperC> <luksMapperD>

# Mount btrfs file system (note can pick any LUKS mapper in the RAID array)
mount <luksMapperA> /mnt

# Create btrfs subvolumes for special directories we-F�d like on separate mounts
btrfs subvolume create /mnt/@root
btrfs subvolume create /mnt/@var
btrfs subvolume create /mnt/@home
btrfs subvolume create /mnt/@snapshots

# Mount our btrfs subvolumes and setup directory tree
umount /mnt
mount -o noatime,compress=lzo,space_cache,subvol=@root <luksMapperA> /mnt
mkdir /mnt/{boot,var,home,.snapshots}
mount -o noatime,compress=lzo,space_cache,subvol=@var <luksMapperA> /mnt/var
mount -o noatime,compress=lzo,space_cache,subvol=@home <luksMapperA> /mnt/home
mount -o noatime,compress=lzo,space_cache,subvol=@snapshots <luksMapperA> /mnt/.snapshots

# Mount our boot partition
mount <boot_partition> /mnt/boot

# Install debootstrap on live USB
apt install debootstrap
apt install schroot
apt install software-properties-common

#schroot config in /etc/schroot/schroot.conf
[Focal]
description=Ubuntu
directory=/mnt
users=root
groups=sbuild
root-groups=root

#execute debootstrap
debootstrap --arch=amd64 focal /mnt http://archive.ubuntu.com/ubuntu/

# prep mounts
mount -o bind /dev /mnt/chroot/dev
mount -t sysfs /sys /mnt/chroot/sys
mount -t proc /proc /mnt/chroot/proc
cp /proc/mounts /mnt/chroot/etc/mtab
cp /etc/resolv.conf /mnt/chroot/etc/resolv.conf


# chroot, NOTE you can also just chroot /mnt/chroot /bin/bash
schroot -c Yakkety -u root /bin/bash

#locale
locale-gen en_US.UTF-8
update-locale LC_ALL=en_US.UTF-8 LANG=en_US.UTF-8

# Add repos
apt-get install software-properties-common
add-apt-repository universe
add-apt-repository 'deb http://security.ubuntu.com/ubuntu/ focal-updates main restricted universe'
add-apt-repository 'deb http://security.ubuntu.com/ubuntu/ focal-security main restricted universe'
apt update

#crypttab
<luks_mapper> UUID=<partition> none luks

# fstab
#<system>        <dir>         <type>    <options>             <dump>      <pass>
<boot_partition>  /boot             ext2    defaults
<efi_partition>     /boot/efi         vfat    defaults
<luksMapperA>   /                    btrfs    subvol=/@root,defaults,noatime     0  0
<luksMapperA>   /home           btrfs    subvol=/@home,defaults,noatime  0  0
<luksMapperA>   /var               btrfs    subvol=/@var/www,noatime          0  0
<luksMapperA>   /.snapshots  btrfs    subvol=/@.snapshots,noatime        0  0

#install btrfs, cryptsetup, linux kernel, linux firmware
# NOTE MUST INSTALL grub-efi-amd64!
apt install grub-efi-amd64 btrfs-progs cryptsetup linux-generic linux-firmware

#install our boot loader, including EFI vars and data
#NOTE: if EFI partition is NOT mounted on /boot/efi, you must tell grub-install where it is
# grub-install <boot_disk> --efi-directory=/mnt/efi
grub-install <boot_disk>
update-grub

#root passwd
Passwd

# Setup Ubuntu network via netplan
https://ubuntu.com/server/docs/network-configuration

# exit and try it out
exit
umount -R /mnt/chroot
reboot

# Continue with system application setup/config

# Setup user
useradd -G sudo -m <your_user>
passwd <your_user>

# Setup development tools
sudo apt install git build-essential cmake

# Disable systemd-networkd in favor of NetworkManager
sudo systemctl stop systemd-networkd
sudo systemctl disable systemd-networkd
sudo systemctl enable NetworkManager
sudo systemctl start NetworkManager

# Setup KDE
sudo apt install kde-plasma-desktop

# KDE config
Setup bash, neovim, tmux (https://github.com/sk3l/movein)
Install auxiliary packages
kde-config-plymouth
plasma-widgets-addons
keepassxc
kcalc
spectacle
ksysguard
gimp
kate
libreoffice
hplip
vlc
gwenview
okular
Filezilla
plasma-nm



