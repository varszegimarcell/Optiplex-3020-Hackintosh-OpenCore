# Dell Optiplex 3020 Hackintosh Catalina 10.15
This repository contains a guide on how to install macOS Catalina on the Dell Optiplex 3020, with the neccesary files.

## Intro

The Dell OptiPlex 3020 is a common and cheap computer, based on the 4th gen Intel Core CPU family, and the Intel Series 8 chipset. When I got my hands on mine, I had no idea what to do with it, but based on a suggestion of my roommate, I've decided to turn it into a Hackintosh. As it turned out, it is a fully capable Hackintosh candidate, with all functionalities working. In this guide, I gonig to explain you the installation process, as well as some considerations in the hardware configuration, to make you a perfect Mac computer, cheaply.

## About the version of OpenCore, and the kext files

Since I'm an active Hackintosh user on this machine, I will update the EFI periodically here as anything new is available.

Currently, this EFI is based on OpenCore 0.6.2, and includes the latest kexts as of 2020.10.29.

## Before you start

### Requirements

You must have the following stuff to make this project successful:
* a Dell OptiPlex 3020 computer, ( Obviously :D ) I have the SFF form factor, this is the machine I've tested the EFI on. This guide and EFI should also work on the 3020 M, and 3020 MT too, without any modifications. [To indentify your 3020, check this link.](https://www.dell.com/support/article/hu-hu/sln288646/optiplex-3020-visual-guide-to-your-computer?lang=en)
* Access to a working Mac/Linux/Windows machine.  
* A 16 GB pendrive. Keep in mind, during the preperation we will format the disk to create the install media, so back up your valuable files.
* Peripherals to interact with your computer.
* Displayport cable, since the VGA port will not work.
* 1-2 hours of free time, and patience.

### Optional hardware upgrades

You should consider some hardware upgrades to this machine, to archive the best results possible. In this section, we'll going through all the possible upgrades you might want to consider for the machine.

#### CPU

By default, my machine came with an Intel i3-4150 CPU, which is fine, but not ideal for a Hackintosh machine. This may vary between machines, so check your configuration.

The problem is with the i3-4150 CPU is that it has Intel HD4400 graphics, which will work, but it will have some small issues, which I explain in this guide later. No Mac computer was shipped with this iGPU, so it is not suprising that it has issues. **Consider upgrading to a CPU, that has Intel HD4600 graphics instead**, Haswell CPUs are pretty cheap on the used market. Ideally, **I would install an i5-4590S,** since we're going to fake our machine as an iMac 15,1 and this machine was shipped with this exact CPU. If you decide to use a HD4400 equiped CPU, we will need to add some tweaks to our config.plist later. **Keep in mind, the cooling solution included in the machine is rated to 65W, so please check the TDP rating of your new CPU.**

#### RAM

By default, my machine came with 4 GB of memory, which is fine, but far from perfect. Althrough Mac will run and work, you will experience some applications closing in the background, and some browser tabs will reload when you switching between them. **I recommend using at least 8 GB RAM,** and using 16 GB will hurt nobody. :) 
The SFF computer I use has 2 RAM slots, it was populated by one 4 GB Hynix HMT451U6AFR8C-PB 1600MHz stick. 

#### SSD

**I highly recommend to install a SATA 3 SSD, to make things more speedy.** My computer came with a Seagate SSHD, with 500GB of capacity. Since optical drives pretty much dead at this point, I've decided to keep disconnected mine, and install an SSD instead. On the drive tray, you have space for 2 2,5" drives, so no need to remove the optical drive. I've installed the main system on the SSD, and I use the SSHD for backups. I would recommend using Samsung SSDs.

#### Networking card

The onboard Gigabit Ethernet is working perfectly, so no need to install an ethernet card, unless you want to use some fancy 10Gb card. **I would recommend to install a Wifi+Bluetooth card with the Broadcomm BCM94360CD chipset.** Althrough it is not required for a functioning system, it is needed to have Continuity, AirDrop, Handoff etc. The best part is, that no workarounds needed if you use this chipset. **They're pretty inexpensive on EBay, but *always read the description,* because some chinese dudes will want you to rip off, and send you a card with a compatible, but not the exactly same chipset.** Compatible chipsets will not work out-of-the box. **If the description contains text like *"maximum chipset"*, do not buy from that seller.** I recommend this chipset, because it was included in the iMac15,1. Some other chipsets may work too, for more information, please read [OpenCore's Wifi Buyers guide.](https://dortania.github.io/Wireless-Buyers-Guide/)

#### Dedicated GPU
You can freely use any decicated GPU supported by MacOS. Keep in mind, this machine's PSU don't have any PCIe power headers, so use a GPU that will work with the power coming from the PCIe socket itself. (Under 75 watts GPUs.) I'm using the iGPU, since don't need any graphics intensive applications. It will work just fine for everything, other than video rendering/CAD/3D modeling/gaming. 

## Preperation 

### Creating the install media

You have to make an installation media to install MacOS, and make it bootable too. If you're using MacOS, you can head to the App Store to download it directly from Apple. Since I had no access to a Mac, I used a script called [giMacOS](https://github.com/corpnewt/gibMacOS), which can also run under Windows. The process on Windows is the following:
* Download gibMacOS from GitHub as a ZIP.
* Extract the gibMacOS-master folder.
* Run gibMacOS.bat, and choose otpion 1 to download the latest Catalina installer.
* After the download succeeded, launch makeInstall.bat, enter the path of the previously downloaded files, (it will be in the script's folder) and choose you USB drive as an output drive. Wait until the process ends, it will take a while.
After the install media was created, we will need to make it bootable later. At the moment, only Macs can boot it. 

### Configuring the EFI

First of all, download the EFI folder I included in this repo, so we can make some adjustments on it. In this step, we will tweak our bootloader, than write it out on the install media's EFI folder, so PCs will be able to boot from it. 

#### GenSMBIOS values

We need a script, called [GenSMBIOS](https://github.com/corpnewt/GenSMBIOS), to be able to fake create fake serial number, UUID, and MLB numbers. **This step is essential to have a working iMessage, so do not skip it.** This script can run on Windows too. The process on windows is the following:
* Download GenSMBIOS as a ZIP, then extract it.
* Launch GenSMBIOS.bat, and use option 1 to download MacSerial.
* Choose option 2, to select the path of the config.plist file. It will be located in EFI/OC folder. ( The stuff you've downloaded from this repo. :) )
* Choose option 3, and enter iMac15,1 as the machine type. 
* Press Q to quit, your config.plist now should contain the required serials.

#### Enter the proper ROM value

After adding serials to your config.plist, you have to add the computer's MAC address to the config.plist file. **This step is essential to have a working iMessage, so do not skip it.** If you have a functioning operating sysytem on it, it is a pretty easy task to accomplish. Since I had no OS on my OptiPlex, I booted into BIOS, and used an smartphone app called [Fing](https://www.fing.com/products/fing-app) to scan the MAC address of the machine. You just have to be in the same network with the machine. Write down the address somewhere, since we'll need it in the next step.

We need a Plist editior, to write the MAC address into the config.plist file. I used [ProperTree](https://github.com/corpnewt/ProperTree), since it works on Windows too. Xcode editior works well too. You have to change the MAC address value in the config.plist at 

    PlatformInfo -> Generic -> ROM

Delete the generic 112233445566 value, and enter your MAC address into the field, without any colons. Save the Plist file, and it is now ready to be written out to the EFI partition of your install media. 

#### Intel HD4400 (You must do this if you have this iGPU only!!!)

In case you've decided to stick with an Intel HD4400, you have to add some more tweaks into the config.plist file. The reason behind this is to force MacOS to use this GPU by injecting fake PCIe ID to it, which makes Mac believe that it is a HD4600. We need to add a new dictionary under the

    DeviceProperties -> Add
    
field, named

    PciRoot(0x0)/Pci(0x2,0x0)
    
We need to add five elements to that dictionary, all items must be data type. The field names and values are the following, seperated by an arrow. (->)

    AAPL,ig-platform-id -> 0300220D
    framebuffer-patch-enable -> 01000000
    framebuffer-stolenmem	-> 00003001
    framebuffer-fbmem	-> 00009000
    device-id	-> 12040000

After adding these values, save your config.plist, and it is now ready to be written out to the EFI partition of your install media.
> Note: I highly recommend not to use an Intel HD4400 equiped CPU, as you'll experience random graphics glitches and freezes in certain apps. Post install setup app is one of them, as you will have random black boxes, Preview will freeze with large image files, as well as GarageBand in the audio settings. (At least, until now, these are the apps that are surely affected by this issue.) For a flawless MacOS experience, I strongly recommend to use Intel HD4600 equipped CPU, or one of the supported dGPUs.

### Writing out the EFI to the install media

After you've finished with the neccesary tweaks, you have to copy the EFI folder to the USB drive's EFI partition. This allows us to boot the MacOS installer on a PC. On Mac and Linux, this partition should be automatically mounted, if not, use the mount command in the terminal to mount it manually. On Windows, follow [this](https://www.insanelymac.com/forum/topic/311820-guide-mount-and-access-efi-partition-on-windows-10/) guide to mount the EFI partition of the USB stick. Copy the entire EFI folder to the drive and, after that we are ready to start working on the installation process.

## Installation

After we've successfully created the install media, we now can install MacOS. We need a few tweaks before that can successfully happen. First, we head to the BIOS settings.

### BIOS settings

* Firstly, you need to load the factory defaults into the BIOS.
* After that, make sure that loading legacy ROMs are enabled, otherwise we'll have sleep issues.
* Make sure that the computer is in UEFI mode. 

The other settings aren't so important, you can set these stuff as you would like.

### Setting NVRAM variables

Sadly, Dell just hidden some neccesary BIOS options from us, which we can adjust only by using NVRAM variables. To make easier this, I've included a modGRUBShell.efi file in the Tools folder. We can just simlpy boot into this GRUB shell, and make the needed adjustments from there with a few commands. 

* Boot into the USB drive. OpenCore should launch now, giving you some boot options.
* Quickly press an up or down arrow, because OC will boot the default option after a few seconds, and we are not ready to boot the installer yet.
* If you've booted Clover on this machine before, choose ClearNvram.efi to clear the NVRAM variables.
* Select modGRUBShell.efi

Now you should be in a grub shell, which is indicated by the 

    grub>

prompt. We need to execute all the commands below.

Disable CFG Lock:

    setup_var 0xDA2 0x0

Set DVMT pre-alloc to 64MB

    setup_var 0x263 0x2

Enable EHCI hand-off

    setup_var 0x2 0x1
    setup_var 0x144 0x1
    setup_var 0x146 0x0
    setup_var 0x147 0x0

Reboot

    reboot

We're done with the NVRAM variables, now we can start the installation process. 

### Installing MacOS

After the reboot, select the Install macOS Catalina (external) boot option. The MacOS installer now should boot. The installation process is the following:

* Select disk utility
* In the view menu, choose show all devices
* Higlight the drive that you want to install MacOS to
* Click erase, change the name as you want, use APFS format, with GUID partition scheme.
* Wait until the process finishes.

You can close disk util now, and select install MacOS. The process is now the same as you wolud do it on a Mac.

After the first reboot, OC will start the installer on your internal disk, you should see a black screen with an Apple logo, and a progress bar, it can take up to 30 minutes.

After the setup, your computer will reboot again, and you should see the welcome setup. Finish it and now you have a working MacOS, althrough we have to do some work to make the OS bootable standalone, without the USB disk.

> If you use Intel HD4400, the welcome setup may have graphic glitches, and you will not be able to see the buttons. If you click in the approximate place where the buttons should be, you can proceed with the welcome setup.

## Post install

We need an app called [EFI Agent](https://github.com/headkaze/EFI-Agent) to be able to mount the internal drive's EFI partition. Mount the internal drive's EFI partiton, copy the contents of the USB drive's EFI partition into there, which is automatically mounted. Unmount your EFI and your USB drive, and after that reboot. Your machine should be able to boot by itself now. 

## Congratuations! Now you have a fully functional MacOS install. 

If iMessage says "contact with apple support" on a login attempt, call Apple spuuort, and tell them that your 5K iMac is unable to log into iMessage, and they'll fix it for you. :D It helps if you have a credit card added to your Apple ID, so they know you're not a fake person who is using a Hackintosh.

Special thanks to [zearp](https://github.com/zearp), without his guide, mine wouldn't be possible.  
