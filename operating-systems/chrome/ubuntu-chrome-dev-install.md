
## Enabling ChromeOS for Multiple Containers

This guides walks you through the process of initializing and bootstrapping a Linux container of your choosing (well, a constrained choice based on available images at the LXC server site described bellow), hosted inside a VM running on your ChromeOS system.

Note that this guide is a algamation of several separate documents on the subject. I've cited the source materials at the bottom of this document.

1. Open Chrome and type: ```chrome://flags#crostini-multi-container```
1. In the resulting window (Figure A), click the Default drop-down associated with Allow Multiple Crostini Containers and select Enabled. 
1. Restart the Chromebook

## Installing Custom Container Image

### Create Container from OS Image

1. From Settings, click on Advanced `>` Developers `>` Linux development environment
1. There should be a menu entitled 'Managed extra containers'; click it
1. You'll se a table entitled 'All containers' that lists existing containers. 
   
   Click the button entitled 'Create' on the right side of the screen

![image](https://user-images.githubusercontent.com/4662876/216786253-ebe488fd-e92a-4c60-92cd-93d0407337ee.png) 

4. A dialog appears entitled 'Create a new container';  fill in the values like so:

![image](https://user-images.githubusercontent.com/4662876/216786476-51a6580c-5cbc-49c1-90fe-b89d5cb2af8a.png)
  
  - `Container name` : descriptive name for your Linux container
  - `VM name` : default should be `termina`; leave this value in place
  - Click 'Advanced' pull down menu
  - `Image Server` : https://us.lxd.images.canonical.com
  - `Image Alias` : '[OS-name]/[version]' e.g. `ubuntu/jammy`

5. Click 'Create' button

### Enter New Container

Start by entering the Chrome shell (crosh) by pressing CTRL+ALT+T, then enter the default termina VM:

```
vmc start termina
```

Enter the newly created container (as root) via a Bash session 

```
lxc exec {container_name} -- bash
```

### User Group Transfer

Capture group membership for default ubuntu user, then delete user

Create a little script which we will use later to add your username to all the default Ubuntu groups, then delete the default ubuntu user:
```
groups ubuntu >update-groups
sed -i 'y/ /,/; s/ubuntu,:,ubuntu,/sudo usermod -aG /; s/$/ \$USER/' update-groups
killall -u ubuntu
userdel -r ubuntu
sed -i '/^ubuntu/d' /etc/sudoers.d/90-cloud-init-users
```

### Install Crostini Repositories

Now we must install the necessary Google cros support packages in our container.

Prepare for installing Google's Crostini specific packages. First bring Ubuntu up to date:
```
apt update
apt upgrade
```

Now add the Crostini package repository to apt. This repository provides the Linux integration with Chrome OS:
```
echo "deb https://storage.googleapis.com/cros-packages bullseye main" > /etc/apt/sources.list.d/cros.list
if [ -f /dev/.cros_milestone ]; then sudo sed -i "s?packages?packages/$(cat /dev/.cros_milestone)?" /etc/apt/sources.list.d/cros.list; fi
apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 78BD65473CB3BD13
apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 4EB27DB2A3B88B8B
apt update
```

### Prepare Crostini packages for installation

A work-around is needed for a cros-ui-config package installation conflict. 

```
apt download cros-ui-config # ignore any warning messages
mkdir tmp
dpkg-deb -R cros-ui-config*.deb tmp
rm ./tmp/etc/gtk-3.0/settings.ini
vi ./tmp/DEBIAN/conffiles # rm the /etc/gtk-3.0/settings.ini line then save and exit. Also delete any blank lines at the end of this file before saving.
dpkg-deb -b tmp cros-ui-config-fixed.deb
````

### Install Crostini packages

apt install ./cros-ui-config-fixed.deb cros-guest-tools
Finishing up

Now, shut down the container:

shutdown -h now

Reboot Chrome OS and start the Terminal application from the launcher. If it fails to start the first time, try again and it should work.

## Links
[Arch Linux instructions](https://wiki.archlinux.org/title/Chrome_OS_devices/Crostini)

[Ubuntu Linux instructions](https://www.reddit.com/r/Crostini/wiki/howto/run-ubuntu/#wiki_install_crostini_packages)

[LXC Images Server](https://us.lxd.images.canonical.com/)

[KDE Plasma instructions](https://itsfoss.com/install-kde-on-ubuntu/)


