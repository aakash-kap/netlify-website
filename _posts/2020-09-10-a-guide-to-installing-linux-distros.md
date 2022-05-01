---
layout: post
title:  "A Fairly Complete Guide to Installing Linux Distros"
author: aakash
tags: [ Linux, Guide ]
image: assets/images/linux_install.png
---

95% of all Linux installing and dual boot guides on the internet are moot. They are either outdated or copies of the same article and most of them do not provide any rationale behind why we do the steps we do. There's a reason why Arch users are so crazy about the Wiki : it is one of the most comprehensive and detailed guides ever written. However, it is certainly too complicated for a new or inexperienced user. This post is an attempt from my side to create a tutorial for installing most general Linux distros that come with a graphical installer while providing slightly more detail than typical tutorials. 

*While I claim that this guide is complete, I recognise that I have not discussed installing from the command line. This is because installing from the command line tends to be a fairly complicated deal unless you are experienced with linux. In addition, apart from distributions like Arch or minimal installers, you would be hard pressed to find non-graphical installers. In addition, a lot of the points mentioned below by me apply even to the non-graphical installers with the simple difference that instead of selection via clicks, you'll need to do it via commands. The guide still applies to you and might be really useful even in this case.*  


#### Pre-Requisites 

1. Make sure you have the image file of the distro *(that you want to install)* downloaded. If you're uncertain about which distro to choose, check out [my recommendations](../linux-distro-choosing-guide/)
2. Now that you have your Linux distro's image file, you need to make bootable USB drive or, for those who still use these, a bootable DVD from it. While this is not the only possible way *(an alternative exists for power users in which you can manually partition your disk and copy over the contents of the Linux distro's image file to the partition. For more details, see [this AskUbuntu answer](https://askubuntu.com/questions/484434/how-can-i-install-ubuntu-without-cd-and-usb))*, it certainly is the most convenient and has a certain degree of safety to it, especially if non-advanced users are involved. This can be done by using tools like [Rufus](https://www.balena.io/etcher/) or [Etcher](https://www.balena.io/etcher/). If you are already running a Linux distro, you can also create the bootable media using the ```dd``` command (refer to the ```man``` pages of the command for more info)
3. Finally, make sure that you have enough free space in your hard disk. You will need to use your current OS's disk partitioning tool to shrink your current partitions and leave enough unallocated space for your Linux install. 

*Hint: For Windows, you can use the in-built  Disk Management tool. You may refer to this guide [here](https://www.digitaltrends.com/computing/how-to-partition-a-hard-drive-in-windows/) if you face any doubts. For Linux, you can use the GParted tool. If confused, you can take help of [this amazing guide](https://www.lifewire.com/use-gparted-to-partition-hard-drive-2205693).*


#### Booting into the Live Environment

1. Now, we need to boot into the bootable media that you created previously. Before we do that, though, there are some things we need to get out of the way. It is always a good idea to open the BIOS and have a look so that any futher pitfalls may be avoided. You can usually open the BIOS by repeatedly pressing **F2** or **F12** keys at boot. If your OEM uses a different key, feel free to [Duck It!](https://duckduckgo.com/).
2. Navigate to the section in the BIOS where you select your boot device order. We will need to ensure that your USB has a higher priority than your Internal Hard Disk where the earlier OS has a higher priority. If you do not do this, your USB may not boot and your installation won't even start. *Note that if you have installed and are using a bootloader like [rEFInd](https://www.rodsbooks.com/refind/), it will detect your USB without you needing to change boot order*
3. While still in the BIOS, make sure that your drive operation mode *(would be named something like Operation Mode/SATA Operation)* should preferably either be **AHCI** or **IDE**. If you have a **RAID** mode, you might have problems but that is not an issue. Linux will work properly with RAID. However, issues arise if your drive is operating in a FakeRAID, something that a lot of mid-range laptops do. The spotting signs of this are if your drive operation mode shows up current setting as **RST (Rapid Store Technology)** or equivalents. 
4. If your drive is in a FakeRAID configuration, the best way forward *(the one that will avoid future problems)* is to switch your drive mode to **AHCI/IDE**. Note that if you were already using Windows before and want to preserve it, as a dual boot maybe, you will need to take precautions. For this, you will have to first ensure that Windows is set to boot into Safe Mode before changing the drive operation mode. After changing the drive mode, boot once into Windows *(it should go into Safe Mode automatically if your settings are correct)*. After that, you can let Windows boot into regular boot again and after a restart, you can follow the same steps as everyone else for continuing with the Linux install. 

*That's it. If you've been successful so far, you should have booted into your bootable medium.*

#### Installing The Linux Distro

1. Now, thanks to all the hard work earlier, things should be quite easy. Graphical installers have come quite a long way in terms of ease and user-friendliness. Let's make good use of that!
2. Choose your settings as you want them to be. There is only one step here that you really need to be careful for and that is the one I shall highlight here. *(Obviously, I don't need to give instructions about what country to select or what time zone or your user name or whatever. That's totally personal :p)*
3. The most important step here is choosing where to install the OS and how to partition the free space. This is the only crucial stop and not being careful here can cause data loss. Don't worry, though. If you do it right, nothing wrong will happen. If you are multi booting, it is best to choose either **Manual Partitioning** or **Something Else** when the installer asks you where to install the OS. Don't let it install in a space it decides of its own. If you are going to keep your new Linux install as the only OS installed then skip over the next step and let the installer handle the partitioning for you.
4. The unallocated space should be split up into the following :
    * If you intend to dual-boot, create a 500 MB partition to be used as the **ESP (EFI System Partition)**. Some installers may not specify this. For such cases, selected the file system type as **FAT32**. Specify the mount point as **/boot/efi**
        * Why do we need to do this? For starters, this is the place where we will be installing our bootloader (GRUB, rEFInd, etc). If we do not do this, the installer will install the bootloader to the Hard Disk root by default. This is not a problem when only using one OS but it becomes a problem when you have installed multiple OSes. 
        * Unless you specify separate bootloader locations for each OS, they will default to using the Hard Disk root. This is the cause for so many problems like updates messing up with boot order and bootloaders *(Remember all the posts titled "Windows erased GRUB? Help!"?)* 
        * This helps you give up very little amounts of space for a much greater sense of security and reliability. I have operated a penta-boot system without issue using this little trick. It works.
    * If you have a device with low RAM *(8 GB or less)*, you could allocate around half that amount to a **Swap Partition**. Note that this should not be done if you have an SSD. Swap is used as a sort of a virtual RAM *(to explain in layman terms)* and thus incurs a lot of write cycles. Such excessive writes to disk will deteriorate SSDs super fast and might cause issues. 
        * There are studies that regardless of HDD or SSD, something like 2 GB of Swap helps. However, I have not done this personally and so cannot vouch for this. In addition, with devices having 16 GB RAM becoming fairly common, this beecomes unneeded.
    * Of the remaining space in the unallocated partition, you can choose to either keep it all as **/** or split it into **/** and **/home**. Personally, I would recommend the latter. This is because your personal files stay in **/home** while most of the programs and the OS you install stay in **/**. This added modularity helps you reinstall your OS without losing your personal files
    * For the **Root Partition (/)**, I would recommend a minimum of 50 GB space for a system you would want to use for serious work *(and not for flicking around)*. You can also make do with as low as 20 GB but that's all your choice.
    * For the **Home Partition (/home)**, I would personally use 50 GB or more. However, as this only stores your files *(which can be stored elsewhere)* and user configuration files *(which are pretty light)*, you can make do with 15 GB as well. 
    * The filesystem for both the Root and Home Partitions can be chosen according to your choice. The default, and most trusted, choice currently is **ext4**. However, there are great alternatives like **btrfs** *(popularity called as ButterFS or BetterFS)* and **zfs** which have good redundancy and data recovery features. 
5. Go ahead, confirm that the partition table has been setup correctly, and proceed to the next step. Enter your personal details, region, keyboard, etc whatever the installer asks you. 


That is it. You'll very soon get the "Successfully Installed" message. Now you can safely eject the bootable medium and reboot. 

Yeah, you can stop reading now. This guide is over. Finally! 
