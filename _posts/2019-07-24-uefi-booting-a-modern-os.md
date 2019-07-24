---
layout: post
title: UEFI - Booting a Modern OS
---

# UEFI - Booting a Modern OS

> *definition* : **The Unified Extensible Firmware Interface (UEFI) is that layer between operating system and firmware providing an environment for running pre-boot and boot applications.**

```text
------------------------
```

The Linux kernel supports [EFISTUB](https://wiki.archlinux.org/index.php/EFISTUB#Using_UEFI_directly) booting which allows UEFI firmware to load the kernel as an UEFI executable meaning that a kernel can be booted directly by a UEFI motherboard without the need for an external boot loader such as grub thus improving the speed and simplicity of the overall boot process.

> 

Modern UEFI systems also now support [retaining](https://wiki.archlinux.org/index.php/Silent_boot#Retaining_the_vendor_logo_from_BIOS) the vendor logo while the system initialises providing for a very good looking boot process indeed. 

>

On Asus ROG motherboards this means having the ROG logo displayed from power on to login screen - about 9 seconds on my Openbox based desktop...

![ROG Logo](/images/02/rog-logo.jpg) 

```text
------------------------
```



# UEFI - Configuring the EFI System

>

Hold down the **Del** key during the cold boot process to enter the UEFI BIOS.

>

On [my current](https://www.asus.com/Motherboards/ROG-STRIX-H270F-GAMING/overview/) motherboards for both PC1 and PC2 the two onboard M.2 pcie drives are populated:

> * one is used for the system **[ / ]**
* the other is used for user data **[ /home ]**

>

The first SATA drive connection is attached to a drive bay to use a hot pluggable rotational drive for backup purposes.

```text
------------------------
```

* set the system time to [UTC](https://time.is/UTC) - the default system base for Linux.

>

<a href="/images/02/efi-1.jpg"><img src="/images/02/efi-1.jpg" width="600"/></a>

```text
------------------------
```

* enable a hot plug sata device for backup purposes.

>

<a href="/images/02/efi-2.jpg"><img src="/images/02/efi-2.jpg" width="600"/></a>

>

<a href="/images/02/efi-3.jpg"><img src="/images/02/efi-3.jpg" width="600"/></a>

>

```text
------------------------
```

* set boot configuration options

>

<a href="/images/02/efi-4.jpg"><img src="/images/02/efi-4.jpg" width="600"/></a>

>

<a href="/images/02/efi-5.jpg"><img src="/images/02/efi-5.jpg" width="600"/></a>

>

```text
------------------------
```

* disable CSM to utilise EFISTUB

>

<a href="/images/02/efi-6.jpg"><img src="/images/02/efi-6.jpg" width="600"/></a>

>

<a href="/images/02/efi-7.jpg"><img src="/images/02/efi-7.jpg" width="600"/></a>

```text
------------------------
```

* disable secure boot to ignore secure keys

>

<a href="/images/02/efi-8.jpg"><img src="/images/02/efi-8.jpg" width="600"/></a>

>

<a href="/images/02/efi-9.jpg"><img src="/images/02/efi-9.jpg" width="600"/></a>

>

```text
------------------------
```

# UEFI - Configuring the EFI Boot Menu

> 

This is an Intel[^1] based Linux **only** system, so we can create a single entry in the EFI boot menu and boot without delay on startup.

>

The [efibootmgr](https://github.com/rhboot/efibootmgr) and [intel-ucode](https://wiki.archlinux.org/index.php/Microcode) packages are installed and used to create both an early update microcode image then clean and create the default EFI boot menu.

>

```bash
$ yay -S efibootmgr intel-ucode
```

>

```bash
$ tree --charset=ascii --noreport -h /boot
/boot
|-- [ 20M]  initramfs-linux.img
|-- [2.5M]  intel-ucode.img
`-- [5.9M]  vmlinuz-linux
```
>

```bash
$ sudo efibootmgr
BootCurrent: 0000
Timeout: 1 seconds
BootOrder: 0000,0001
Boot0000* Arch
Boot0001  UEFI: Generic-SD/MMC 1.00, Partition 1
```

>

```bash
$ sudo efibootmgr -b 0001 -B
$ sudo efibootmgr -b 0000 -B
$ sudo efibootmgr
BootCurrent: 0000
Timeout: 1 seconds
No BootOrder is set; firmware will attempt recovery
```

>

```bash
$ sudo blkid | grep system 
/dev/nvme0n1p2: LABEL="system" UUID="3ae12e2f-87c5-430d-b08f-ce49ca0bd52d" UUID_SUB="548d4366-79b1-4e0e-a1dd-309c00b20588" TYPE="btrfs" PARTLABEL="system" PARTUUID="1cdd2278-a978-45fb-917d-c70809a94ed3"
```

> 

```bash
$ sudo efibootmgr -c -d /dev/nvme0n1 -p 1 -L "Arch" -l /vmlinuz-linux -u "root=PARTUUID=1cdd2278-a978-45fb-917d-c70809a94ed3 rootfstype=btrfs rootdelay rw quiet systemd.log_level=0 rd.systemd.show_status=0 rd.udev.log_priority=0 audit=0 initrd=\intel-ucode.img initrd=\initramfs-linux.img"
BootCurrent: 0000
Timeout: 1 seconds
BootOrder: 0000
Boot0000* Arch
```

```text
------------------------
```

*For more detailed information on this process see the next post ...*

```text
------------------------
```

# KEY

[^1]: [Intel Microcode](https://en.wikipedia.org/wiki/Intel_Microcode)
