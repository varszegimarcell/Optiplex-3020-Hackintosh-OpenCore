# Dell Optiplex 3020 Hackintosh OpenCore

This repository contains a guide on how to install macOS on the Dell Optiplex 3020, with the neccesary files, using OpenCore bootloader.

This guide covers the following macOS versions:
* macOS 11 Big Sur

## Disclaimer

As I am a human being, like you, I make mistakes. I'm not responsible for any negative consequences, if you follow this guide, and something goes wrong. If you find any issues, feel free to open a ticket, or send me a message. Use this guide at your own responsibility.

## Intro

The Dell Optiplex 3020 is a common and cheap business computer, based on the 4th gen Intel Core CPU family, and the Intel Series 8 chipset. When I got my hands on mine, I had no idea what to do with it, but based on a suggestion of my roommate, I've decided to turn it into a Hackintosh. As it turned out, it is a fully capable Hackintosh candidate, with all functionalities working. In this guide, I gonig to explain you the installation process, as well as some considerations in the hardware configuration, to make you a perfect Mac computer, cheaply.

## About the version of OpenCore, and the kext files

Since I'm an active Hackintosh user on this machine, I will update the EFI periodically here as anything new is available.

Currently, this EFI is based on OpenCore 0.6.7, and includes the latest kexts as of 2021.03.09.


## About Big Sur

With this EFI, you can install Big Sur. I started this guide in the Catalina days, now I only cover Big Sur, as it is pretty stable at this point. This EFI may work with Catalina too, although I don't test it anymore. 

If you want to update from Catalina, go to the System Preferences, check for updates and just install it. 

> NOTICE: Some users (including me) experienced a problem when tried to update Catalina to Big Sur from System Preferences, as after the install, the machine booted back to Catalina, like nothing was changed. I have no fixes for this issue at the moment, if the System Preferences update fails, you have to update via an install USB.

**About INTEL HD4400 with Big Sur:** With the latest kexts, Big Sur is now usable with Intel HD4400. Sadly, it is far from bug free, some weird graphical glitches can happen all the time. I noticed, that sometimes, folder icons show up with a file icon too for example, rendered buggy on top of the folder icon. Also, Preview can freeze with large (4K) image files. I have sometimes issues in GarageBand too. I still strongly recommend to use Intel HD4600 instead, but at least is is usable with HD4400 too. 

## Before you start

### Requirements

You must have the following stuff to make this project successful:
* a Dell Optiplex 3020 computer, ( Obviously :D ) I have the SFF form factor, this is the machine I've tested the EFI on. This guide and EFI should also work on the 3020 M, and 3020 MT too, without any modifications. [To indentify your 3020, check this link.](https://www.dell.com/support/article/hu-hu/sln288646/optiplex-3020-visual-guide-to-your-computer?lang=en)
* Access to a working Mac/Linux/Windows machine.  
* A 16 GB pendrive. Keep in mind, during the preperation we will format the disk to create the install media, so back up your valuable files.
* Peripherals to interact with your Hackintosh computer.
* Displayport cable, since the VGA port will not work.
* 1-2 hours of free time, and patience.

### Optional hardware upgrades

You should consider some hardware upgrades to this machine, to archive the best results possible. In this section, we'll going through all the possible upgrades you might want to consider for the machine. If you're not interested in hardware upgrades, you can skip this section, but I strongly recommend to use a CPU with Intel HD4600 at least.

#### CPU

By default, my machine came with an Intel i3-4150 CPU, which is fine, but not ideal for a Hackintosh machine. This may vary between machines, so check your configuration.

The problem with the i3-4150 CPU is that it has Intel HD4400 graphics, which will work, but it will have some small issues, which I explain in this guide later. No Mac computer was shipped with this iGPU, so it is not suprising that it has issues. **Consider upgrading to a CPU, that has Intel HD4600 graphics instead**, LGA1150 Haswell CPUs are pretty cheap on the used market. Ideally, **I would install an i5-4590S,** since we're going to fake our machine as an iMac 15,1 and this machine was shipped with this exact CPU. **Keep in mind, the cooling solution included in the machine is rated to 65W in case of the 3020 MT and SFF, and 35W in case of the 3020M. Please check the TDP rating of your new CPU. Also, keep in mind the capabilities of the power supply.**

#### RAM

By default, my machine came with 4 GB of memory, which is fine, but far from perfect. Althrough Mac will run and work, you will experience some applications closing in the background, and some browser tabs will reload when you switching between them. **I recommend using at least 8 GB RAM for optimal multi-tasking experience,** and using 16 GB will hurt nobody. :) DDR3 1600MHz RAM sticks are pretty cheap too these days. 
The SFF computer I use has 2 RAM slots, one was populated with a 4 GB Hynix HMT451U6AFR8C-PB 1600MHz stick. 

#### SSD

**I highly recommend to install a SATA 3 SSD, to make things more speedy.** My computer came with a Seagate SSHD, with 500GB of capacity. Since optical drives pretty much dead at this point, I've decided to keep disconnected mine, and install an SSD instead. On the drive tray, you have space for two 2,5" drives, so no need to remove the ODD. I've installed the main system on the SSD, and I use the SSHD for backups. I would recommend using Samsung SSDs.

#### Networking card

The onboard Gigabit Ethernet is working perfectly, so no need to install an ethernet card, unless you want to use some fancy 10Gb cards. **I would recommend to install a Wifi+Bluetooth card, with the Broadcomm BCM94360CD chipset.** Althrough it is not required for a functioning system, it is needed to have Continuity, AirDrop, Handoff etc. The best part is, that no workarounds needed if you use this chipset, it's plug'n'play. **They're pretty inexpensive on EBay, but *always read the description,* because some chinese dudes want to rip you off, as they will send you a card with a compatible, but not the exactly same, and slower chipset.** Compatible chipsets will not work out-of-the box, so they will need some "kext magic" to be able to make them work. I see no reason to get a random card for slightly cheaper price, and it will casue more headaches for sure. **If the description contains text like *"maximum chipset"*, do not buy from that seller.** I recommend this exact chipset, because it was included in the iMac15,1, and has WIFI+BT on the same PCIe card. Some other chipsets may work too, for more information, please read [OpenCore's Wifi Buyers guide.](https://dortania.github.io/Wireless-Buyers-Guide/)

I've installed a Fenvi T919 Wifi+BT card. MacOS recognizes it as an AirportExtreme card, and works perfectly. Keep in mind, you may need to log out and log back into your Apple ID, becasue continuity services and handoff will not work. Of course, if you install the card before you install macOS, no such problem will exist. If you install the card later, this should fix this issue. I had to do this on my iPhone too, but it was my fault, as I've removed the card that I made work before, and reinstalled macOS later. 

#### Dedicated GPU
You can freely use any decicated GPU that supports by MacOS. Keep in mind, this machine's PSU don't have any PCIe power headers, so use a GPU that will work with power coming from just the PCIe socket. (Under 75 watts.) ~~Upgrading PSU may be an option, but getting one for the SFF and M form factors are pretty difficult.~~ I'm using the iGPU, since don't need any graphics intensive applications. It will work just fine for everything, other than video rendering/CAD/3D modeling/gaming.

> Note on the PSU upgrade: as the computer uses ATX12VO standard PSU, it will be extremely difficult to find a proper replacement for it, so I cannot recommend to upgrade the PSU. 

## Preperation

### Update your BIOS

First of all, install the latest BIOS on your 3020 machine. For more instructions, please check [Dell's website.](https://www.dell.com/support/home/hu-hu/drivers/driversdetails?driverid=ptjjd&oscode=w764&productcode=optiplex-3020-desktop)

I made a bootable FreeDOS USB, and copied over the exe file that Dell provided with the update. Run this executable, and follow the instructions. Keep in mind, you need to enable legacy boot for FreeDOS, which we'll need to turn off later.

### Creating the install media

You have to make an installation media to install MacOS, and later you have to make it bootable by PCs too. If you're using MacOS, you can head to the App Store to download the installer directly from Apple.

#### Creating Big Sur (11) install media on Windows

Since gibMacOS is broken with Big Sur, making an install media on Windows is now more difficult, than it was on Catalina. OpenCore has a nicely detailed guide about the topic, check it out on [this link.](https://dortania.github.io/OpenCore-Install-Guide/installer-guide/winblows-install.html#downloading-macos) Keep in mind, this method creates a web installer only, so make sure you have active internet connection during the install. 

For Mac and Linux, check [this link.](https://dortania.github.io/OpenCore-Install-Guide/extras/big-sur/#installation)

### Configuring the EFI

First of all, download the EFI folder I've included in this repo, so we can make some adjustments on it. You will find the latest files under the releases, or just simply download the repo as it is.

In this step, we will tweak our bootloader, generate our fake Mac serials, than write it out on the install media's EFI folder, so PCs will be able to boot from it. Althrough generating serials are possible post-install, I would not recommend it, doing these tasks now can save a lot of pain and troubleshooting later, especially when you trying to make iMessage work. 

#### GenSMBIOS values

We need a script, called [GenSMBIOS](https://github.com/corpnewt/GenSMBIOS), to be able to create fake serial number, UUID, and MLB numbers. **This step is essential to have a working iMessage, so do not skip it. It is highly recommended to do this now, rather than post-install.** This script can run on Windows too. 

The process is the following:
* Download GenSMBIOS as a ZIP, then extract it.
* Launch GenSMBIOS.bat, (or GenSMBIOS.command on Mac) and use option 1 to download MacSerial.
* Choose option 2, to select the path of the config.plist file. It will be located in EFI/OC folder. ( The stuff you've downloaded from this repo. :) )
* Choose option 3, and enter iMac15,1 as the machine type. This is the closest Mac configuration to our Hackintosh machine. 
* Press Q to quit, your config.plist now should contain the required serials.

#### Enter the proper ROM value

After adding serials to your config.plist, you have to add the computer's MAC address to the config.plist file. **This step is also essential to have a working iMessage, so do not skip it. It is highly recommended to do this now, rather than post-install.** If you have a functioning operating sysytem on the Optiplex, it is a pretty easy task to accomplish. Since I had no OS on my Optiplex, I booted into BIOS, and used an smartphone app called [Fing](https://www.fing.com/products/fing-app) to scan the MAC address of the machine. You just have to be in the same network with the machine. Write down the address somewhere, since we'll need it in the next step.

We need a Plist editior, to write the MAC address into the config.plist file. I used [ProperTree](https://github.com/corpnewt/ProperTree), since it works on Windows too. Xcode editior works well too. You have to change the MAC address value in the config.plist at 

    PlatformInfo -> Generic -> ROM

Delete the generic 112233445566 value, and enter your MAC address into the field, without any colons. Save the Plist file, and it is now ready to be written out to the EFI partition of your install media. 

#### About the Intel HD4400 iGPU

I highly recommend not to use an Intel HD4400 equiped CPU, as you'll experience random graphics glitches and freezes in certain apps. Mac setup app (on first boot) is one of them, as you will have random black boxes on the screen. Preview will freeze with large image files, as well as GarageBand freezes in the audio settings menu. (At least, until now, these are the apps that I experienced issues with.) For a flawless MacOS experience, I strongly recommend to use Intel HD4600 equipped CPU, or one of the supported dGPUs.

#### If you will add a dGPU

In this guide, I'm not covering how to add a dedicated GPU, as it would be different for different GPUs. You have to do your own research here. However, you must change some iGPU configuration for sure. First, head to the

    DeviceProperties -> PciRoot(0x0)/Pci(0x2,0x0)

dictionary, in the config.plist file. Then, remove these entries:

    disable-external-gpu
    disable-hdmi-patches
    enable-hdmi20
    framebuffer-patch-enabled
    framebuffer-fbmem
    framebuffer-stolenmem
    framebuffer-unifiedmem
    hda-gfx

After that, modify the following value like that:

    AAPL,ig-platform-id -> 04001204

This will put you iGPU into compute-only mode, and it will be used for encoding tasks only by macOS. In this state, the iGPU is not capable to drive a display.

### Writing out the EFI to the install media

After you've finished with the neccesary tweaks, you have to copy the EFI folder to the USB drive's EFI partition. This allows us to boot the MacOS installer on a PC. On Mac and Linux, this partition should be automatically mounted, if not, use the mount command in the terminal to mount it manually. On Windows, follow [this guide](https://www.insanelymac.com/forum/topic/311820-guide-mount-and-access-efi-partition-on-windows-10/) to mount the EFI partition of the USB stick. (Although the one created by gibMacOS mounted automatically under Windows too.) Copy the entire EFI folder to the drive - including the root EFI folder too - and, after that we are ready to start working on the installation process. If you've followed OpenCore's media creation guide, use this EFI folder insted their generic one. 

## Installation

After we've successfully created the install media, we now can install MacOS. We need a few tweaks before that can successfully happen. First, we head to the BIOS settings, then we have to set some NVRAM values.

### BIOS settings

* Firstly, you need to load the factory defaults into the BIOS.
* After that, make sure that loading legacy ROMs are enabled, otherwise we'll have sleep issues.
* Make sure that the computer is in UEFI mode. 

The other settings aren't so important, you can set these stuff as you would like.

### Setting NVRAM variables

Sadly, Dell just hidden some neccesary BIOS options from us, which we can adjust only by using NVRAM variables. To make easier this, I've included a modGRUBShell.efi file in the Tools folder. We can just simlpy boot into this GRUB shell, and make the needed adjustments from there with a few commands. 

* Boot into the USB drive. OpenCore should launch now, giving you some boot options.
* Quickly press an up or down arrow, because OC will boot the default option after a few seconds, and we are not ready to boot the installer yet.
* If you've booted Clover on this machine before, choose Reset NVRAM to clear the NVRAM variables. It is the last boot option in OpenCore.
* Select modGRUBShell.efi

Now you should be in a grub shell, which is indicated by the 

    grub>

prompt. We need to execute all the commands below. Pay special attention to run the commands that are meant for your computer. If you mismatch these commands, reset the motherboard, as applying the wrong variables will cause undefinied behavior. See note after section how to do it. Keep in mind, resetting the NVRAM via OpenCore will not affect these values.

**In case of 3020 MT and 3020 SFF computers** 

Disable CFG Lock:

    setup_var 0xD9E 0x0 

Set DVMT pre-alloc to 64MB

    setup_var 0x263 0x2

Enable EHCI hand-off

    setup_var 0x2 0x1
    setup_var 0x144 0x1
    setup_var 0x15A 0x2
    setup_var 0x146 0x0
    setup_var 0x147 0x0

Reboot

    reboot

**In case of 3020 M computer** 

Disable CFG Lock:

    setup_var  0x53 0x0 

Set DVMT pre-alloc to 64MB

    setup_var 0x2F2 0x2

Enable EHCI hand-off

    setup_var 0x2 0x1
    setup_var 0x187 0x1
    setup_var 0x1A4 0x2
    setup_var 0x189 0x0
    setup_var 0x18A 0x0

Reboot

    reboot


We're done with the NVRAM variables, now we can start the installation process. 

> Note: If you want to revert these changes, or you've "bricked" your computer take out the CMOS battery of the motherboard, short the RTCST jumper, with the jumper looted from the password reset jumper, and connect the computer to the power chord. Leave it like for a minute. After that, unplug the computer, and press the power button for 30 secs. Reinstall the password reset jumper to it's original place, and the CMOS battery. Your machine is now resetted. Check out this article on [Dell's site](https://www.dell.com/support/manuals/hu-hu/optiplex-3020-desktop/opt3020sffom-v1/system-board-components?guid=guid-fa8ac1e1-18ef-46eb-bb0b-39ac2e5d41fe&lang=en-us), to find these stuff on your motherboard. 

### Installing MacOS

After the reboot, select the macOS Base System (external) boot option. The MacOS installer now should boot. The installation process is the following:

* Select disk utility.
* In the view menu, choose show all devices.
* Higlight the drive that you want to install MacOS to.
* Click erase, change the name as you want, (Mac SSD etc.) use APFS format, with GUID partition scheme.
* Wait until the process finishes.

You can close disk util now, and select install MacOS. The process is now the same as you wolud do it on a Mac.

After the first reboot, OC will start the installer on your internal disk, you should see a black screen with an Apple logo, and a progress bar, it can take up to 30 minutes.

After the setup, your computer will reboot again, and you should see the welcome setup. Finish it and now you have a working MacOS, althrough we have to do some work, to make the OS bootable without the USB disk.

> If you use Intel HD4400, the welcome setup may have graphic glitches, and you will not be able to see the Next buttons. If you click in the approximate place where the buttons should be, you can proceed with the welcome setup.

## Post install

We need an app called [EFI Agent](https://github.com/headkaze/EFI-Agent) to be able to mount the internal drive's EFI partition. Mount the internal drive's EFI partiton, copy the contents of the USB drive's EFI partition into there, which is automatically mounted. Unmount your EFI and your USB drive, and after that reboot. Your machine should be able to boot by itself now. 

## Congratuations! Now you have a fully functional MacOS install. 

If iMessage says "contact with apple support" on a login attempt, call Apple spuuort, and tell them that your 5K iMac is unable to log into iMessage, and they'll fix it for you. :D It helps if you have a credit card added to your Apple ID, so they know you're not a fake person who is using a Hackintosh. For further iMessage troubleshooting, please visit [Tonymacx86's guide here](https://www.tonymacx86.com/threads/how-to-fix-imessage.110471/)

## Thank you
Special thanks to [zearp](https://github.com/zearp), without his guide, mine wouldn't be possible, especially at setting the NVRAM variables.  
Special thanks to [jayphizzle](https://github.com/jayphizzle), his donation made me able to buy a new CPU, after my old one died.
