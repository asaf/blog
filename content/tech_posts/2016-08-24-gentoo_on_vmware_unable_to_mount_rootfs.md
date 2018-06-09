---
tags:
- gentoo
- linux
date: "2016-08-24T13:26:30+03:00"
title: "Gentoo On Vmware Fusion - unable to mount rootfs"

---

I've been trying to install Gentoo linux as a guest on OSX using Vmware Fusion,
After configuring the kernel, booting the system resulted:


Using the Gentoo minimal bootable disc, we can identify what SCSI storage controller we have:

```bash
lspci |grep -i scsi
00:10.0 SCSI storage controller: LSI Logic / Symbios Logic 53c1030 PCI-X Fusion-MPT Dual Ultra320 SCSI (rev 01)
```

The _Ultra 320_ controller requires [Fusion-MPT](http://cateee.net/lkddb/web-lkddb/FUSION.html) support in the kernel, that is located under:

*Device Drivers -> Fusion MPT device support - Fusion MTP ScsiHost drivers for SPI*


Don't forget to:

```bash
make;make install;grub-mkconfig /dev/sda
```

Also note that although Gentoo handbook defines _initramfs_ is an optional it is mandatory for MTP to work properly.


For more info, please refer to: http://wikigentoo.ksiezyc.pl/HOWTO_Install_Gentoo_on_VMware.htm#2.6.x_Kernel_Configuration

Re-install the kernel and reboot.