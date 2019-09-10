# Steps 

## VM Creation in Virtual Box

* create shell of VM in Virtual Box
 * establish name
 * set RAM size
 * set HDD properties
  * type (should use VMHD; virtual machine hard disk)
  * size

* refine VM properties 
 * right-click Virtual Box VM in left hand pane; select 'Settings'
 * CPU
  * set processor count to desired value under "System -> Processor"
 * Video
  * should set minimum 64MB memory
  * might want to increase "Scale Factor" to 200% for initial installation for better viewing of console
 * Audio
  * just disable audio for server installation
 * Network
  * create a _second_ network adapter for local (VM to VM) connection
   * "Attached to" => "Host-only Adapter"

* mount installation medium (e.g. iso DVD) in VM
 * right-click Virtual Box VM in left hand pane; select 'Settings'
 * select Storage
 * ensure the "IDE Controller" entry exists, and contains an entry (click '+' to add one, if needed)
 * click the desired entry
  * select "Live CD/USB" option
  * in "Optical Drive" entry, select desired .iso image from host system file pathe

## RHEL Installation

* commence VM installation (example is Red Hat Enterprise Linux 7)
 * click 'Start' from top level menu
 * click 'Install Red Hat Enterprise Linux 7'
  * select installation language ('English'); press 'Continue'
  * press 'Software Selection' to customize initial installed application. For generic server, pick 'Minimal Install' and select following options:
   * Development Tools
   * System Administration Tools
   * Security Tools
  * press 'Installation Destination' to customize storage volume options
   * choose 'LVM' partitioning scheme
   * add desired mount points, with desired sizes
    * boot (/boot); type = ext2; _standard partition, NOT LVM_
    * root (/); type = ext4; size >= 20GiB is best
    * home (/home); type = ext4; size >= 20GiB; _encrypted volume is ideal_
    * var (/var); type = ext4; size >= 10GiB
    * _set LUKS passphrase for /home mount_
  * press 'Network Installation' to customize network options
   * change hostname if desired
   * change NAT network connection name to mnemonic one e.g. `elan`
   * change Host network connection name to mnemonic one e.g. `ehst`
  * press 'Begin Installation'
   * set `root` user password
   * create a default non-`root` user
    * select as administrator
    * set password 

* for remote SSH access, create a network port forward to the VM via VirtualBox "NAT" network interface

## RHEL Post-install Setup

* create a `$HOME/.local` directory for local dev stuff

* make user account `sudo` without password
 * `sudo visudo`, uncomment line like:
```
%wheel ALL=(ALL) NOPASSWD: ALL
```

* correctly assign Network Manager connections to device; they seem to be disconnected upon VM creation
```
nmcli c delete <NAT conn name>
nmcli c dlete <local host conn name>
nmcli c add type ethernet con-name enat ifname enp0s3 
nmcli c add type ethernet con-name ehst ifname enp0s8
``` 
reboot; connections should be attached by default

* establish appropriate YUM repos in `/etc/yum.repos.d`
```
sudo cp cloud.repos /etc/yum.repos.d
```
disable gpg check in `/etc/yum.conf` if desired

* update to latest version of software
```
sudo yum upgrade -y
```

* generate SSH keypair for access
```
mkdir ~/.ssh
chmod 700 ~/.ssh
cd ~/.ssh
ssh-keygen -t rsa -b 2048 -C $USER_at_$HOST -f $USER_at_$HOST
cat ~/.ssh/$USER_at_$HOST >> ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
```

* install useful developer tools

  [ ] newer Git (2.x+) - SCL?
  
  [ ] newer ViM (8.x+) - compile from source?
  
  [ ] ViM Vundle, plugins
  
  [ ] newer tmux       - compile from source?
  
  [ ] Clang/LLVM (for ViM syntastic)
  
  [ ] CMake3
  
  [ ] devtoolset-7 g++, gdb - SCL
  
  [ ] python36, python36-pip, python36-setuptools
  
  [ ] python -> pylint, jedi-vim, virtualenvwrapper
  
  [ ] tree
  
  [ ] tidy

