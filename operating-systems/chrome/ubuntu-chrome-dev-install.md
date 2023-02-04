
## Enabling ChromeOS for Multiple Containers

1. Open Chrome and type: ```chrome://flags#crostini-multi-container```
1. In the resulting window (Figure A), click the Default drop-down associated with Allow Multiple Crostini Containers and select Enabled. 
1. Restart the Chromebook

## Installing Custom Container Image

1. From Settings, click on Advanced `>` Developers `>` Linux development environment
1. There should be a menu entitled 'Managed extra containers'; click it
1. A table entitled 'All containers' lists existing containers
1. Click the button entitled 'Create' on the right side of the screen
1. A dialog appears entitled 'Create a new container'; input the following values:
  - `Container name` : descriptive name for your Linux container
  - `VM name` : default should be `termina`; leave this value in place
  - Click 'Advanced' pull down menu
  - `Image Server` : https://us.lxd.images.canonical.com
  - `Image Alias` : '[OS-name]/[version]' e.g. `ubuntu/jammy`
  - Click 'Create' button

## Links
[Arch Linux instructions](https://wiki.archlinux.org/title/Chrome_OS_devices/Crostini)
[Ubuntu Linux instructions](https://www.reddit.com/r/Crostini/wiki/howto/run-ubuntu/#wiki_install_crostini_packages)
[LXC Images Server](https://us.lxd.images.canonical.com/)
[KDE Plasma instructions](https://itsfoss.com/install-kde-on-ubuntu/)


