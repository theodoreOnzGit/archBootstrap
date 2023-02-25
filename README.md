# archBootsrap
A collection of config files and scripts to help with arch bootstrap 
while installing arch from exting linux, eg. manjaro, endeavourOS or even linux mint

## Purpose

Installing Arch Linux is challenging as it is.

Of the many methods to use to install Arch Linux, 
I have chosen to install it from my 
[existing](https://wiki.archlinux.org/title/Install_Arch_Linux_from_existing_Linux) 
linux manjaro distribution.

1. Why bootstrap and not a liveCD?

Because I have a working GUI, web browser and network to search through
all the Arch Linux Documentation I need.

There is also a method to use [a live cd image](https://wiki.archlinux.org/title/Install_Arch_Linux_from_existing_Linux#Method_B:_Using_the_LiveCD_image)
 to install arch. Unfortunately, you kind of need to know how to
 unsquash an image and mount it properly in order to use it.

 This means we download the iso image, extract the contents and
 mount it directly rather than boot into the liveCD.

 Can be quite a steep elarning curve. So i'd rather avoid that.

## Step 1: Downloading and Setting Up the Bootstrap Environment For Arch-Chroot

1. First it's important to [download](https://archlinux.org/download/)
 the bootstrap environment.
Just click on any mirror and start downloading the bootstrap tar.gz
file straightaway.

- [archlinux-bootstrap-2022.06.01-x86_64.tar.gz](http://mirror.arizona.edu/archlinux/iso/2022.06.01/archlinux-bootstrap-2022.06.01-x86_64.tar.gz)
- This is the example of one in 1st jun 2022. 
- Supposing the image is safe, use:
```zsh
sudo tar xzf <path-to-bootstrap-image>/archlinux-bootstrap-*-x86_64.tar.gz --numeric-owner
```
- next, mount the system using sudo, this is to help pacman install packages [properly](https://wiki.archlinux.org/title/Install_Arch_Linux_from_existing_Linux#Downloading_basic_tools)

```zsh
sudo mount --bind directory-to-livecd-or-bootstrap directory-to-livecd-or-bootstrap
```

For a bootstrap, just navigate to the folder where your tar
file is and you ran the xzf command and run:
```zsh
sudo mount --bind ./root.x86_64/ ./root.x86_64/
```

2. Setting up pacman mirrors

Now, this bootstrap environment doesn't have vim or any nice color schemes.
So it's important to install these things before we start.

It doesn't even have pacman setup properly.

Hence, we will want to setup pacman first, especially the mirrors
as some mirrors will be outdated and will cause key signing errors.

The traditional way is to go to pacman-mirrors and uncomment them using
vim.

```zsh
sudo vim ./etc/pacman.d/mirrorlist
```

You would then uncomment the lines here and be done with it. However, 
try that and you would most certainly run into key signing errors.

Rather, go to [arch linux mirrors](https://archlinux.org/mirrorlist/), 
setup the mirror list and then copy and paste it into your mirrorlist 
file.

## Arch-Chroot and Setup Pacman
Once you've setup the archlinux bootstrap environment, and mirrors,

Once that is done, run:

```zsh
sudo ./root.x86_64/bin/arch-chroot ./root.x86_64/ 
```

You are actually running the binary arch-chroot which is in the 
bootstrap environment.

What this does is that it makes you the root user as using the
bootstrap root directory as your root directory.

To exit out of chroot, use
```zsh
exit
```

Once you're comfortable, start setting up pacman.

```zsh
pacman-key --init
pacman-key --populate
pacman -Syyu
```

Now of course, the terminal looks very ugly. There's no vim, and you
can't really do much here.

## Scripts for Quality of life improvements

To make the terminal a little nicer and to enable vim usage,

1. I've included a .bashrc file to add some color and conveneint aliases
2. I've included a .vimrc file which gives basic numbering
3. I've added a .zshrc file for convenient aliases too
4. I've added a script to automate installation of 
- vim
- base-devel
- zsh and a lot of other nice packages
- git

Just clone this git directory and execute the scripts:
### pacman installs
#### This part helps update pacman and vim:
```zsh
pacman-key --init
pacman-key --populate

# immediately after we must update the keyring and 
# and update the packages

pacman -S archlinux-keyring --needed --noconfirm
pacman -Syyu --needed --noconfirm

# first thing is to install vim, super important!
pacman -S vim --needed --noconfirm
```

####This part installs base-devel and git
```zsh

# then let's install base-devel and git
pacman -S base-devel git --needed --noconfirm 
```

#### boot and formatting tools setup

now to install bootloaders and such, we need to install the 

```zsh

# to use mkfs.fat we need dosfstools
pacman -S dosfstools --needed --noconfirm

# grub tools
pacman -S os-prober grub efibootmgr --needed --noconfirm
```
#### For zsh, here we are going to use:
```zsh
# finally let's install all the zsh stuff and give the terminal and pacman nice colors
pacman -S grml-zsh-config zsh-autosuggestions zsh-completions --needed --noconfirm
pacman -S zsh-history-substring-substring-search --needed --noconfirm
pacman -S zsh-syntax-highlighting --needed --noconfirm
pacman -S zsh-theme-powerlevel10k --needed --noconfirm
echo 'source /usr/share/zsh-theme-powerlevel10k/powerlevel10k.zsh-theme' >>~/.zshrc
echo 'alias l="ls -aCF"' >>~/.zshrc
echo 'alias pacman="pacman --color=auto"' >> ~/.zshrc
exec zsh
```

This will enable you to use zsh with nicer colors after you set 
everything up.
#### .bashrc and .vimrc setup
Now once you should also copy the .bashrc and .vimrc from this repo into your ./root folder
this will enable you vim and use the bash shell with decent colors.

```zsh
# copy .vimrc and .bashrc to /root/.
cp ../.vimrc /root/.
cp ../.bashrc /root/.
```

#### ranger setup
now if you are like me and like vim, install ranger
it will allow you to browse files extremely quickly
using vim keybindings

```zsh
# ranger: a vim like command line file manager
pacman -S ranger --needed --noconfirm
```
[Go back to main page](https://github.com/theodoreOnzGit/archlinuxBootstrapInstall)

#### cfdisk or fdisk
 
Quite important for partitioning disks right!

# Step 2: cfdisk or fdisk, format and mount

What you want to do is:
1. Create a boot partition, maybe 500 MB, set as EFI (I'm not covering BIOS)
2. Create an ext4 partition for root. 
3. Format (X = disk, Y = partition number), check using lsblk
```zsh
mkfs.fat -F 32 /dev/sdXY
mkfs.ext4 /dev/sdXY
```

# Step 3: pacstrap arch-linux and other stuff

TBCTBC






















