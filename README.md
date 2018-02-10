# My Debian 9 (Stretch) Setup

This is my nimble computer setup - a very lightweight Debian 9.3 (stretch) installation with Openbox and other cool little tweaks.

It has been configured on a [Dell Inspiron 11 3000 (Pentium N3710 Quad-Core) laptop](http://www.dell.com/au/p/inspiron-11-3162-laptop/pd?oc=z511231au&model_id=inspiron-11-3162-laptop).

## Clean
![Alt text](desktop-clean.png?raw=true "Desktop Clean")

## Busy
![Alt text](desktop-busy.png?raw=true "Desktop Busy")

## Window Snapping Example
![Alt text](window-snapping-example.png?raw=true "Window Snapping")

## Window Snapping Demo
![Alt text](window-snapping-demo.gif?raw=true "Window Snapping")

## Window Snapping Shortcuts

`Super Key (Win Key) + Up` - Resizes window to 100% width and 100% height

`Super Key (Win Key) + Right` - Snaps window to the right at 50% width and 100% height

`Super Key (Win Key) + Down` - Resizes window to 50% width and 50% height

`Super Key (Win Key) + Left` - Snaps window to the left at 50% width and 100% height

`Super Key (Win Key) + Alt + Up` - Snaps window to the top at 50% width and 50% height

`Super Key (Win Key) + Alt + Right` - Snaps window to the right at 50% width and 50% height

`Super Key (Win Key) + Alt + Bottom` - Snaps window to the bottom at 50% width and 50% height

`Super Key (Win Key) + Alt + Left` - Snaps window to the left at 50% width and 50% height

## Install Debian Stretch

Go to the [Debian downloads](https://www.debian.org/CD/netinst/) and grab the latest amd64 NetInstall.

Once downloaded, open up terminal and make a bootable USB:

    sudo dd bs=4m if=debian-9.1.0-amd64-netinst.iso of=/dev/disk# && sync

## Installing Network Tools and Drivers

Before proceeding any further, make sure you login as root.

### Install Drivers:

Clone the following repo:

    git clone https://github.com/OpenELEC/iwlwifi-firmware.git

Create a firmware directory on your linux machine:

    cd /lib/ && mkdir firmware

Copy all the contents of firmware from the Github repo and put it in `lib/firmware` on your linux machine.

### Install Wireless Tools

Download and copy the following packages onto a USB. Mount the USB and install the following packages:

- [wpa_supplicant](https://packages.debian.org/stretch/amd64/wpasupplicant)
- [wireless-tools](https://packages.debian.org/stretch/amd64/wireless-tools)

## Configure Network

    nano /etc/network/interfaces

Replace the contents below `source /etc/network/interfaces.d/*` with:

    auto wlp1s0
    iface wlp1s0 inet dhcp

## Connect to Wireless

Create a new wpa_supplicant config:

`wpa_passphrase 'YOUR_NETWORK_NAME' NETWORK_PASSWORD >> /etc/network/interfaces`

Now edit `/etc/network/interfaces` to look like below (make sure to remove `network={...` and the other weird formatting:

    auto wlp1s0
    iface wlp1s0 inet dhcp
        wpa-ssid NETWORK_NAME_FROM_SSID_WITHOUT_QUOTES
        wpa-psk KEY_GENERATED_FROM_PSK

run the following commands:

    ifup wlp1s0

    dhclient wlp1s0

Try pinging google.com `ping google.com`. If it works, you're in business! :)

## Updating Sources

`nano /etc/apt/sources.list`

    deb http://ftp.us.debian.org/debian/ stretch main contrib non-free
    deb-src http://ftp.us.debian.org/debian/ stretch main contrib non-free

    deb http://security.debian.org/debian-security stretch/updates main contrib non-free
    deb-src http://security.debian.org/debian-security stretch/updates main contrib non-free

    # stretch-updates, previously known as 'volatile'
    deb http://ftp.us.debian.org/debian/ stretch-updates main contrib non-free
    deb-src http://ftp.us.debian.org/debian/ stretch-updates main contrib non-free

## Upgrade to Kernel 4.15.2

The current kernel on Debian Stretch 4.9.X doesn't play nicely with `xorg` on this dell laptop, so you will need to upgrade it to a more recent version.

Copy the following files to USB and install them on your machine.

    wget kernel.ubuntu.com/~kernel-ppa/mainline/v4.15.2/linux-headers-4.15.2-041502_4.15.2-041502.201802072230_all.deb

    wget kernel.ubuntu.com/~kernel-ppa/mainline/v4.15.2/linux-headers-4.15.2-041502-generic_4.15.2-041502.201802072230_amd64.deb

    wget kernel.ubuntu.com/~kernel-ppa/mainline/v4.15.2/linux-image-4.15.2-041502-generic_4.15.2-041502.201802072230_amd64.deb

    sudo dpkg -i linux-headers-4.15.2*.deb linux-image-4.15.2*.deb

    systemctl reboot

## Cleaning Up Old Kernels
  
Get List of Current Installed Kernels 

    dpkg --list | grep linux-image

Remove Old Kernel Images and Headers

    sudo apt-get purge linux-image-x.x.x-x

    sudo apt-get purge linux-headers-x.x.x-x

Update Bootloader

    sudo update-grub2

Reboot

    sudo reboot

## Install Packages

Once you are connected to the internet, upgrade your system and install the packages below:

    apt-get update && apt-get upgrade -y && apt-get install aptitude apt-transport-https libnotify-bin xorg xbacklight openbox alsa-utils volumeicon-alsa lightdm obconf obmenu curl xclip p7zip htop rofi nitrogen tint2 viewnior xcompmgr zip thunar thunar-archive-plugin thunar-media-tags-plugin terminator preload lxappearance lxappearance-obconf git gitk autoconf libgtk-3-dev chromium wicd xscreensaver xscreensaver-gl-extra xscreensaver-data-extra simplescreenrecorder simple-scan gnome-disk-utility ack-grep vlc libssl1.0.2 -y

    reboot

## Add Sudo Privileges
Add your user to the sudo group:

    sudo adduser <username> sudo

## Clone Repo and Copy Config

Clone the debian-setup repo:

    git clone https://github.com/matthewhartman/debian-setup.git

    cd debian-setup

Copy the appropriate configuration files below

    cp -a .config/* ~/.config

    cp .xscreensaver ~/

    cp -a .themes ~/.themes

    cp -a wallpapers ~/Pictures

    sudo cp -a minimal-debian /usr/share/slim/themes/

    sudo cp -a wicd-icons/* /usr/share/pixmaps/wicd

Change the `current_theme` in slim.conf from `debian-lines` to `minimal-debian`

    `sudo nano /etc/slim.conf`

## Install Google Chrome

Install Google's key

    wget -q -O - https://dl.google.com/linux/linux_signing_key.pub | sudo apt-key add -

    sudo nano /etc/apt/sources.list.d/google-chrome.list

Add the following in google-chrome.list:

    deb [arch=amd64] http://dl.google.com/linux/chrome/deb/ stable main

Remove chromium and install Google Chrome

    sudo apt-get update && sudo apt-get remove chromium && sudo apt-get install google-chrome-stable

## Install the ARC Theme

    sudo su

    echo 'deb http://download.opensuse.org/repositories/home:/Horst3180/Debian_8.0/ /' > /etc/apt/sources.list.d/arc-theme.list

    apt-get update && apt-get install arc-theme

    exit

## Install ARC Icon Theme

    git clone https://github.com/horst3180/arc-icon-theme --depth 1 && cd arc-icon-theme

    ./autogen.sh --prefix=/usr

    sudo make install

## Get rid of hanging network task

    nano /etc/network/interfaces

Replace the contents below `source /etc/network/interfaces.d/*` with:

    auto wlp1s0

## Fix PcSpkr Issue on Boot (optional)

If you see this error on boot `Error: Driver 'pcspkr' is already registered, aborting` - You can fix it by adding `blacklist pcspkr` to `/etc/modprobe.d/blacklist.conf`

## Install Sublime Text 3
Install the GPG key:

    wget -qO - https://download.sublimetext.com/sublimehq-pub.gpg | sudo apt-key add -

Select the stable version:

    echo "deb https://download.sublimetext.com/ apt/stable/" | sudo tee /etc/apt/sources.list.d/sublime-text.list

    sudo apt-get update && sudo apt-get install sublime-text

Once you have downloaded and installed sublime, get up to speed with the following packages / config

- Install Package Control
- Install [HyperClick](https://github.com/aziz/SublimeHyperClick) manually
- Create `Default (Linux).sublime-mousemap` in `~/.config/sublime-text-3/Packages/User` and put in the following contents:

      [
        {
          "button": "button1",
          "count": 1,
          "modifiers": ["ctrl"],
          "press_command": "drag_select",
          "command": "goto_definition"
        }
      ]

- Create `JavaScript (Babel).sublime-settings` in `~/.config/sublime-text-3/Packages/User` and put in the following contents:

      {
        "extensions":
        [
          "js"
        ]
      }

- Edit User Settings and replace all contents with the following:

      {
        "bold_folder_labels": true,
        "draw_minimap_border": true,
        "font_size": 12,
        "highlight_line": true,
        "highlight_modified_tabs": true,
        "line_padding_bottom": 1,
        "line_padding_top": 1,
        "save_on_focus_lost": true,
        "tab_size": 2,
        "translate_tabs_to_spaces": true,
        "word_wrap": true,
        "folder_exclude_patterns":[".git","node_modules"]
      }

Enjoy Sublime :)


## Install Node Version Manager

    sudo apt-get install build-essential libssl-dev

    curl -sL https://raw.githubusercontent.com/creationix/nvm/v0.32.0/install.sh -o install_nvm.sh

    bash install_nvm.sh

    source ~/.profile

## Install NodeJS and NPM

    nvm install stable

## Install Bash Git Prompt

    cd ~

    git clone https://github.com/magicmonty/bash-git-prompt.git .bash-git-prompt --depth=1

Add to `~/.bashrc`:

    GIT_PROMPT_ONLY_IN_REPO=1
    source ~/.bash-git-prompt/gitprompt.sh

## Enable CTRL + ALT + Backspace
You can restart X by pressing CTRL + ALT + Backpace. To enable this feature, simply run:

    sudo dpkg-reconfigure keyboard-configuration

Go through the steps and at the very end, you will be prompted if you would like to enable CTRL + ALT + Backspace to restart X. Say yes and run `systemctl reboot`

## Fix Brightness - xbacklight

Check the backlight directory (you should see intel_backlight)

    ls /sys/class/backlight

Get the Identifier (I had `0x42`)
    
    xrandr --verbose

Create an xorg.conf if you don't have one

    cd /etc/X11/

    sudo touch xorg.conf

    sudo nano xorg.conf

Add the following to your xorg.conf

    Section "Device"
        Identifier  "0x42"
        Driver      "intel"
        Option      "Backlight"  "intel_backlight"
    EndSection

Reboot the machine

## Install Steam

Enable the 32bit architecture:

    sudo dpkg --add-architecture i386

Install Steam:

    sudo apt install steam

## Counter Strike Source keeps crashing.
Try deleting the sound folder:

    find ~/.steam/steam/ -iname sound.cache -delete

Then in Counter Strike Source, open the terminal and run:

    snd_restart

## Install Greenclip
    
    wget https://github.com/erebe/greenclip/releases/download/2.1/greenclip

Make Greenclip executable

    sudo mv greenclip /usr/bin/

## Enjoy your new setup

Like a boss ;)


            /((((((\\\\
    =======((((((((((\\\\\
         ((           \\\\\\\
         ( (*    _/      \\\\\\\
           \    /  \      \\\\\\________________
            |  |   |       </                  ((\\\\
            o_|   /        /                      \ \\\\    \\\\\\\
                 |  ._    (                        \ \\\\\\\\\\\\\\\\
                 | /                       /       /    \\\\\\\     \\
         .______/\/     /                 /       /         \\\
        / __.____/    _/         ________(       /\
       / / / ________/`---------'         \     /  \_
      / /  \ \                             \   \ \_  \
     ( <    \ \                             >  /    \ \
      \/      \\_                          / /       > )
               \_|                        / /       / /
                                        _//       _//
                                       /_|       /_|
