# PS1 Xploder/Xplorer Setup

This document is intended to serve as a guide to preparing a setup for flashing Xploder modules over a parallel port on modern operating systems. As computers with onboard parallel ports have become increasingly rare and older tools such as Caeflash are only compatible with outdated MS-DOS systems, I wanted an environment that could be easily reproduced on currently accessible hardware and operating systems.

For this setup, I used the following:
- VMWare Workstation 15 Player (Version 15.5.6) running a Windows 7 SP1 x86 guest on a Windows 10 Version 1903 x64 host
- StarTech PEX1P PCI Express Parallel Adapter Card
- GiveIO driver from sickle's patched X-Link: http://www.psxdev.net/forum/viewtopic.php?f=60&t=746
- sickle's patched X-Killer: http://www.psxdev.net/forum/viewtopic.php?t=392

Other hypervisors, guest/host OSes, and PCIe parallel adapters may also work, but have not been personally tested. WINE on Linux may work as well, but this has not been tested either.

## Setting up the PCIe parallel adapter

Install the PCIe parallel adapter on the host OS by following the instructions on this page: https://www.startech.com/en-us/faq/oxford-serial-parallel-install-windows-7810. If using a different adapter, follow the manufacturer's instructions for that particular adapter. In my particular case, the card was installed as LPT3; I changed this to LPT1 through `Device Manager -> Ports (COM & LPT) -> right click PCI Express ECP Parallel Port -> Properties -> Port Settings tab -> change LPT Port Number to LPT1`. I'm not sure if changing to LPT1 is required, but it certainly didn't seem to hurt.

Unfortunately, my parallel port was set to two I/O ranges: one starting at 0x3FF4 and another starting at 0x3FF8, neither of which are part of the three standard base addresses used by X-Killer. This was thankfully resolved by the use of a VM.

## Setting up the VM

For this step, I used a Windows 7 XP1 x86 virtual machine that I already had set up. x64 versions may have issues with the GiveIO driver required for sickle's patched X-Killer, but I have not tested this.

Make sure to add a virtual parallel port device to your VM, which can be done when you create a new VM or alternatively at any time on an existing VM through its settings. If you get an error such as `Failed to connect virtual device 'parallel0'` when booting the VM, you may have to reinstall VMWare to get its parallel port driver to install. VMWare apparently does not install its parallel port driver if no physical parallel ports are detected on the initial installation.

After successfully booting into the VM, a parallel port should be visible under the guest OS's Device Manager with a base address of 0x0378, which is what we want for X-Killer.

## Setting up X-Killer

I wasn't able to get simias's InpOut32 version of X-Killer working in the guest OS, so for this step we'll use sickle's GiveIO version of X-Killer. Acquire sickle's patched X-Link and X-Killer from the links above and install the GiveIO driver included in the X-Link archive. You may have to run LoadDrv in administrator mode for the installation process. LoadDrv will need to be used to start the GiveIO driver each time you want to use X-Killer between guest OS restarts.

At this point, we should now be able to start up X-Killer without any issues. We won't be using X-Link, so feel free to ignore it.

## Backing up and flashing the Xploder

With the Xploder attached to both your PC and console and booted into the Xploder ROM's menu, we can start playing around with X-Killer.

X-Killer has a number of features available for stock Xploder ROMs. To see your ROM version, go to `Tools -> ROM Manager -> Detect`. To see your ROM's supported features, check the README included in the X-Killer folder. The Xploder can also be backed up and flashed through the ROM Manager.

At this point, simias' guide (https://github.com/simias/rustation/wiki/Xplorer-FX-setup) can be followed starting partway through the "Flashing Caetla 0.34" section, around where the ROM Manager window is shown. It is recommended that you back up your stock ROM before you flash over it.

When flashing Caetla, X-Killer may ask if you want to flash as PAL with English. This seems to affect the startup video mode and language. If you select no, Caetla will start up in NTSC with Japanese. Some users have reported not getting video due to Caetla outputting PAL when their television only supports NTSC. To avoid this, you can select no when X-killer asks if you would like to flash as PAL with English. To change the language setting in Caetla from Japanese to English, select `コンフィグ` from the main menu, scroll down to `使用言語` near the bottom, and change `日本語` to `英語`. Exit the configuration menu and Caetla should now be in English.
