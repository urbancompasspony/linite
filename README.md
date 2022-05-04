# linite

# What is it?

For now only in Brazillian Portuguese!

An alternative to NINITE® but for Linux, debian-based machines!

# How to Use it

To use it, boot your linux system and run this:

## curl -sSL https://linite.linuxuniverse.com.br | sudo bash

# Configuring

When running for first time, the code will ask to install a dependency: dialog.

The first question is to remove the most problematic packages on Ubuntu systems or its flavours: 
cloud-init snapd unattended-upgrades blueman bluetooth

Then will autoremove, update and upgrade the system.

After this, you will see the auto-install menu with lots of options!

# MENU

Before installing anything, the menu will show you what will be installed and ask if you want to install.
The options are:

A Virtual
To install all dependencies to run virtual machines through qemu, kvm and virt-manager

B Essentials
Some essential packages to any distribution or arch, even RaspberryPi!

C Server
Server essential packages.

D to VM
Packages exclusively to install on Linux virtual machines.

E Desktop
Packages for Desktop systems like printer drivers and qbittorrent.

F Multimedia
Multimedia packages in general like Blender, Audacity and K3B.

G Games
Packages for gamers, like Steam, WINE and Winetricks.

H Instalar PiHole
This install PiHole® on your system!

I NOROOT Menu
WIP to auto-install a menu on system.
