## How the Linux Kernel Boots

Kernel is the heart of Linux. Its loading from when you press power button to the point when user space is available 
to interact with involves lot of other steps.
This invloves the machines BIOS running the bootloader, the bootloader finding kernel image on disk, loading it on memory
and running it. Kernel then initializing devices and drivers. Kernel then mounts the filesystem, and then starts a 
program called *init* with process ID 1. This process then initializes user space and rest of the system.

#### Startup Messages

The kernel logs messages when it starts, then the *init* process and then the process started after this.
This info is not always usefull and most of the times garbage. 
If you want to view startup logs use
```bash
journalctl -k
```
This is for current sessions bootloader messages. If you want to see previous bootloader's logs use
```bash
journalctl -b
```
You can also find logs in */var/log/kern.log*

#### Kernel Initialization and Boot Options

The kernel initializes in this order(generally)
1. CPU Inspection
2. Memory inspection
3. Device bus discovery
4. Device discovery
5. Auxiliary kernel subsystem setup (networking and the like)
6. Root filesystem mount
7. User space start

#### Kernel Parameters

These are the parameters provided to kernel when it starts. You can view them
```bash
cat /proc/cmdline
```

One of them in root parameter, it provides location of root filesystem
```
root=/dev/sda1
```

When kernel doesn't understand the parameter provided it passes it on to *init* program. Like *-s* is passed
on which tells *init* to run in silent mode.

#### Boot Loaders

Typical job of a boot loader is to find the kernel stored in disk and load it, then run the kernel with parameters.
But before kernel is loaded how does the boot loader even know where the disk space is. This is done with the help
of BIOS(Basic Input/Output System) or UEFI(Unified Extensible Firmware Interface). 

Below is the list of some common bootloaders:
1. GRUB
2. LILO
3. SYSLINUX
4. LOADIN
5. systemd-boot
6. coreboot
7. Linux Plugin
8. efilinux

#### GRUB Introduction

GRUB stands for Grand Unified Boot Loader. GRUB specilizes in filesystem navigation which helps in finding kernel
during boot loading. GRUB has a menu, which can be accessed after switching on the computer and pressing combination
of keys. It has its own kernel, its own modules and its own command sets.

Now we'll see how GRUB works.

1. The PC BIOS or firmware initializes the hardware and searches its bootorder storage devices for boot code.
2. Upon finding the boot code, the BIOS/firmware loads and executes it.
This is where GRUB begins.
3. The GRUB core loads.
4. The core initializes. At this point, GRUB can now access disks and
filesystems.
5. GRUB identifies its boot partition and loads a configuration there.
6. GRUB gives the user a chance to change the configuration.
7. After a timeout or user action, GRUB executes the configuration.
8. In the course of executing the configuration, GRUB may load additional code (modules) in the boot partition. Some of
these modules may be preloaded.
9. GRUB executes a boot command to load and execute the kernel as specified by the configuration’s linux command.

