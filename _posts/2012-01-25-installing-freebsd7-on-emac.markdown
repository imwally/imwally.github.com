---
layout: post
title: Installing FreeBSD7.1 on an eMac 1.42GHz (PowerPC)
categories: [blog]
---

Resources
http://www.freebsd.org/platforms/ppc.html
http://markmail.org/message/hj3cvoc3adxnbbea
http://people.freebsd.org/~grehan/iso_install.txt

What you will need
FreeBSD installation source (I hope you already have this)
Bootable live CD capable of partitioning drives (Mac OS X Install CD will do)

Preface
This is for anyone out there who is seeking to install FreeBSD on their PowerPC machine. I'm writing this out of frustration where I've spent almost 2 days trying to install FreeBSD on my eMac and to help anyone else who might come across the same problem that I have; FreeBSD won't mount the file system correctly in order to continue installation.

Problem
For some reason (which I couldn't find documented anywhere unless I'm blind) FreeBSD/ppc won't mount any file system created with Disklabel's auto defaults. This means that when you're setting up your partitions with FreeBSD's Disklabel and you've selected "A" for auto-defaults the root file system will not mount resulting in installation failure.

Solution
Let's start from the beginning. To properly setup your partitions you will need to boot up either a live Linux distro or if you happen to have any OS X CDs lying around that will work too. I'm going to base this off of using OS X to setup the disks. When you boot up the OS X installation CD (by holding down C) you will first select your language and then you'll notice you have various options in the top menu bar. You want to select Utilities > Disk Utility. Select the hard drive you want to partition from the left hand side and then click on the Partition option on the right. If you want to go with the standard:

UFS (Unix File System) Partitions
/ - fb_root - 512MB
swap - fb_swap - 600MB
/var - fb_var - 750MB
/tmp - fb_tmp - 800MB
/usr - fb_user - Rest of disk space

Then we want to partition are disk into 5 different slices according to the size that you want for each slice. At the very least make sure you have a root partition and a swap. Also, to make things easier you can label each disk as I did above. Once everything looks right then go ahead and click partition.

Now we can boot into FreeBSD and head straight to the dreaded Disklabel where everything ended in disaster before. Instead of having one giant disk that you might have seen before such as:

Disk: ad0 Partition name: ad0s1 Free: xxxxx blocks (xxxxxxxMB)

You will now see something similar to this:

Disk: ad0 Partition name: ad0s1 Free: 63 blocks (0MB)
Disk: ad0 Partition name: ad0s2 Free: 262144 blocks (128MB)
Disk: ad0 Partition name: ad0s3 Free: __n__ blocks (10MB)
Disk: ad0 Partition name: ad0s4 Free: 17408 blocks (8MB)
Disk: ad0 Partition name: ad0s5 Free: __n__ blocks (33000MB)
Disk: ad0 Partition name: ad0s6 Free: 17408 blocks (8MB)
Disk: ad0 Partition name: ad0s7 Free: __n__ blocks (750MB)
Disk: ad0 Partition name: ad0s8 Free: 17408 blocks (8MB)
Disk: ad0 Partition name: ad0s9 Free: __n__ blocks (2000MB)

I pulled the above from one of the top resources listed just to give you an idea. The disk numbers and sizes my vary depending on how you partitioned your disk. You will also see extra 8MB and 10MB partitions, don't worry about these, just ignore them. Just make sure you remember the sizes for which your partitioned your drive meaning your final disk layout should resemble this according to my map listed above:

/ - Disk: ad0 Partition name: ad0s1 Free: xxxx blocks (512MB)
swap - Disk: ad0 Partition name: ad0s2 Free: xxxx blocks (600MB)
/var - Disk: ad0 Partition name: ad0s3 Free: xxxx blocks (750MB)
/tmp - Disk: ad0 Partition name: ad0s4 Free: xxxx blocks (800MB)
/usr - Disk: ad0 Partition name: ad0s5 Free: xxxx blocks (25000MB)

To create a FS highlight a partition such as the 512MB slice, press C then new file system and name it /. This will be your root FS. Just keep going down the line and create a FS for your /var, /tmp, and /usr partitions as well as a swap. Once everything looks right and lines up according to the sizes that you've setup then press Q and you should hopefully see that it successfully mounted the root FS and continues to a normal installation.

Post Installation
In order to boot your fresh FreeBSD install you will need to boot into Open Firmware first by pressing ctrl + option + f + o. Once in Open Firmware you can either boot FreeBSD from the CD boot loader (FreeBSD CD must be in the drive) by typing:

0 > boot cd:,\boot\loader hd:X

Where X is the partition number where the root FS is. In the case above where the 512MB root partition is ad0s1 you would replace hd:X with hd:1.

Summary
FreeBSD/ppc does not like Auto Defaults! You should have your drive partitioned before booting into FreeBSD or the installer won't mount the root FS for installation. I tried to make this as straight forward as possible without actually going through the process, just pulling steps from memory. If I'm missing something or you have any questions please don't hesitate to email me at soytea (at) gmail (dot) com.
