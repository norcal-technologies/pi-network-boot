# pi-network-boot
Network booting Raspberry Pi's

## Overview

This covers network booting Raspberry Pi 3 Model B Plus Rev 1.3 without using a micro SD card on the workers.

```
$ grep Model /proc/cpuinfo 

Model		: Raspberry Pi 3 Model B Plus Rev 1.3
```

## Networking

To stop any potential inteference from DHCP between the local network in your office/home it is highly recommended to run this in an isolated environment, ideally a seperate switch. To allow remote access to the Master node, you can enable WiFi as DHCP will only be running on the wired etherenet (eth0).

### Static IP for Master node

`10.1.1.100`

### DHCP offered to Worker nodes

Worker nodes: `10.1.1.1` to `10.1.1.20`

Worker 1 - 77b655ec
Worker 2 - 
Worker 3 - 8b07bcc6

### Diagram
```

                        +---------------------+
                        |     S W I T C H     |
                        +---------------------+





```
## Prerequisites

### Update OTP on Pi 3 models

The OTP (One Time Programmable) Memory of the Pi 3 model needs updating before you can boot over the network. 

#### Download Pi OS and boot vi micro SD card

To do this you will need to download and install the latest version of Raspberry Pi OS from https://www.raspberrypi.com/software/operating-systems/ on an micro SD card. It is recommended to use the Pi OS Lite.

For this we used the version from 13th May 2025 which is based on Bookworm: 
https://downloads.raspberrypi.com/raspios_lite_arm64/images/raspios_lite_arm64-2025-05-13/2025-05-13-raspios-bookworm-arm64-lite.img.xz

#### Update OTP

Once you have booted open a terminal and run `echo program_usb_boot_mode=1 | sudo tee -a /boot/config.txt`

e.g.
```
$ echo program_usb_boot_mode=1 | sudo tee -a /boot/config.txt
```

#### Reboot

Next you need to reboot to get it to read the value from the config file.

#### Check OTP update

To check it worked, open a terminal and run `vcgencmd otp_dump | grep 17:` to check the OTP value. This should be `17:3020000a`

e.g.
```
$ vcgencmd otp_dump | grep 17:
17:3020000a
```

#### Obtain the Pi Serial number

Obtain the Serial number of the Pi using the following from a terminal:

```
 $ grep Serial /proc/cpuinfo 
Serial		: 000000008b07bcc6
```

## Configure Master Node

* Install packages
* Set up dnsmasq
* Set up tftpboot directories
* Set up nfsroot directories

### Install packages

```
sudo apt install nmap
```

etc.
```
$ sudo systemctl restart dnsmasq
```

## Configure Worker Nodes

Boot the worker node without a micro SD card in. 


```
 $ grep Serial /proc/cpuinfo 
Serial		: 000000008b07bcc6
```

### Syslog

```
2025-09-26T16:25:02.962460+01:00 pi-master dnsmasq-dhcp[5629]: DHCPDISCOVER(eth0) b8:27:eb:07:bc:c6 
2025-09-26T16:25:02.963269+01:00 pi-master dnsmasq-dhcp[5629]: DHCPOFFER(eth0) 10.1.1.2 b8:27:eb:07:bc:c6 
2025-09-26T16:25:03.015438+01:00 pi-master dnsmasq-tftp[5629]: file /tftpboot/bootsig.bin not found for 10.1.1.2
2025-09-26T16:25:03.015777+01:00 pi-master dnsmasq-tftp[5629]: sent /tftpboot/bootcode.bin to 10.1.1.2
2025-09-26T16:25:03.119617+01:00 pi-master dnsmasq-dhcp[5629]: DHCPDISCOVER(eth0) b8:27:eb:07:bc:c6 
2025-09-26T16:25:03.120397+01:00 pi-master dnsmasq-dhcp[5629]: DHCPOFFER(eth0) 10.1.1.2 b8:27:eb:07:bc:c6 
2025-09-26T16:25:03.122503+01:00 pi-master dnsmasq-tftp[5629]: error 0 Early terminate received from 10.1.1.2
2025-09-26T16:25:03.122936+01:00 pi-master dnsmasq-tftp[5629]: failed sending /tftpboot/8b07bcc6/start.elf to 10.1.1.2
2025-09-26T16:25:03.130490+01:00 pi-master dnsmasq-tftp[5629]: file /tftpboot/8b07bcc6/autoboot.txt not found for 10.1.1.2
2025-09-26T16:25:03.131108+01:00 pi-master dnsmasq-tftp[5629]: error 0 Early terminate received from 10.1.1.2
2025-09-26T16:25:03.131453+01:00 pi-master dnsmasq-tftp[5629]: failed sending /tftpboot/8b07bcc6/start.elf to 10.1.1.2
2025-09-26T16:25:03.133801+01:00 pi-master dnsmasq-tftp[5629]: sent /tftpboot/8b07bcc6/config.txt to 10.1.1.2
2025-09-26T16:25:03.137479+01:00 pi-master dnsmasq-tftp[5629]: file /tftpboot/8b07bcc6/recovery.elf not found for 10.1.1.2
2025-09-26T16:25:05.850161+01:00 pi-master dnsmasq-tftp[5629]: sent /tftpboot/8b07bcc6/start.elf to 10.1.1.2
2025-09-26T16:25:05.856889+01:00 pi-master dnsmasq-tftp[5629]: sent /tftpboot/8b07bcc6/fixup.dat to 10.1.1.2
2025-09-26T16:25:06.008035+01:00 pi-master dnsmasq-tftp[5629]: file /tftpboot/8b07bcc6/recovery.elf not found for 10.1.1.2
2025-09-26T16:25:06.010302+01:00 pi-master dnsmasq-tftp[5629]: error 0 Early terminate received from 10.1.1.2
2025-09-26T16:25:06.010759+01:00 pi-master dnsmasq-tftp[5629]: failed sending /tftpboot/8b07bcc6/config.txt to 10.1.1.2
2025-09-26T16:25:06.013097+01:00 pi-master dnsmasq-tftp[5629]: sent /tftpboot/8b07bcc6/config.txt to 10.1.1.2
2025-09-26T16:25:06.014533+01:00 pi-master dnsmasq-tftp[5629]: file /tftpboot/8b07bcc6/dt-blob.bin not found for 10.1.1.2
2025-09-26T16:25:06.076082+01:00 pi-master dnsmasq-tftp[5629]: file /tftpboot/8b07bcc6/recovery.elf not found for 10.1.1.2
2025-09-26T16:25:06.078446+01:00 pi-master dnsmasq-tftp[5629]: error 0 Early terminate received from 10.1.1.2
2025-09-26T16:25:06.078948+01:00 pi-master dnsmasq-tftp[5629]: failed sending /tftpboot/8b07bcc6/config.txt to 10.1.1.2
2025-09-26T16:25:06.080730+01:00 pi-master dnsmasq-tftp[5629]: sent /tftpboot/8b07bcc6/config.txt to 10.1.1.2
2025-09-26T16:25:06.620640+01:00 pi-master dnsmasq-tftp[5629]: file /tftpboot/8b07bcc6/bootcfg.txt not found for 10.1.1.2
2025-09-26T16:25:06.660114+01:00 pi-master dnsmasq-tftp[5629]: file /tftpboot/8b07bcc6/recovery8.img not found for 10.1.1.2
2025-09-26T16:25:06.662407+01:00 pi-master dnsmasq-tftp[5629]: error 0 Early terminate received from 10.1.1.2
2025-09-26T16:25:06.662882+01:00 pi-master dnsmasq-tftp[5629]: failed sending /tftpboot/8b07bcc6/kernel8.img to 10.1.1.2
2025-09-26T16:25:06.663969+01:00 pi-master dnsmasq-tftp[5629]: error 0 Early terminate received from 10.1.1.2
2025-09-26T16:25:06.664358+01:00 pi-master dnsmasq-tftp[5629]: failed sending /tftpboot/8b07bcc6/kernel8.img to 10.1.1.2
2025-09-26T16:25:06.665512+01:00 pi-master dnsmasq-tftp[5629]: error 0 Early terminate received from 10.1.1.2
2025-09-26T16:25:06.666016+01:00 pi-master dnsmasq-tftp[5629]: failed sending /tftpboot/8b07bcc6/initramfs8 to 10.1.1.2
2025-09-26T16:25:06.667160+01:00 pi-master dnsmasq-tftp[5629]: error 0 Early terminate received from 10.1.1.2
2025-09-26T16:25:06.667576+01:00 pi-master dnsmasq-tftp[5629]: failed sending /tftpboot/8b07bcc6/initramfs8 to 10.1.1.2
2025-09-26T16:25:06.668517+01:00 pi-master dnsmasq-tftp[5629]: error 0 Early terminate received from 10.1.1.2
2025-09-26T16:25:06.668944+01:00 pi-master dnsmasq-tftp[5629]: failed sending /tftpboot/8b07bcc6/initramfs8 to 10.1.1.2
2025-09-26T16:25:12.426097+01:00 pi-master dnsmasq-tftp[5629]: sent /tftpboot/8b07bcc6/initramfs8 to 10.1.1.2
2025-09-26T16:25:12.445076+01:00 pi-master dnsmasq-tftp[5629]: error 0 Early terminate received from 10.1.1.2
2025-09-26T16:25:12.445434+01:00 pi-master dnsmasq-tftp[5629]: failed sending /tftpboot/8b07bcc6/bcm2710-rpi-3-b-plus.dtb to 10.1.1.2
2025-09-26T16:25:12.472706+01:00 pi-master dnsmasq-tftp[5629]: sent /tftpboot/8b07bcc6/bcm2710-rpi-3-b-plus.dtb to 10.1.1.2
2025-09-26T16:25:12.489836+01:00 pi-master dnsmasq-tftp[5629]: error 0 Early terminate received from 10.1.1.2
2025-09-26T16:25:12.490216+01:00 pi-master dnsmasq-tftp[5629]: failed sending /tftpboot/8b07bcc6/overlays/overlay_map.dtb to 10.1.1.2
2025-09-26T16:25:12.495919+01:00 pi-master dnsmasq-tftp[5629]: sent /tftpboot/8b07bcc6/overlays/overlay_map.dtb to 10.1.1.2
2025-09-26T16:25:12.529482+01:00 pi-master dnsmasq-tftp[5629]: error 0 Early terminate received from 10.1.1.2
2025-09-26T16:25:12.529754+01:00 pi-master dnsmasq-tftp[5629]: failed sending /tftpboot/8b07bcc6/config.txt to 10.1.1.2
2025-09-26T16:25:12.531638+01:00 pi-master dnsmasq-tftp[5629]: sent /tftpboot/8b07bcc6/config.txt to 10.1.1.2
2025-09-26T16:25:12.540609+01:00 pi-master dnsmasq-tftp[5629]: error 0 Early terminate received from 10.1.1.2
2025-09-26T16:25:12.540887+01:00 pi-master dnsmasq-tftp[5629]: failed sending /tftpboot/8b07bcc6/overlays/vc4-kms-v3d.dtbo to 10.1.1.2
2025-09-26T16:25:12.543957+01:00 pi-master dnsmasq-tftp[5629]: sent /tftpboot/8b07bcc6/overlays/vc4-kms-v3d.dtbo to 10.1.1.2
2025-09-26T16:25:12.702545+01:00 pi-master dnsmasq-tftp[5629]: error 0 Early terminate received from 10.1.1.2
2025-09-26T16:25:12.703077+01:00 pi-master dnsmasq-tftp[5629]: sent /tftpboot/8b07bcc6/cmdline.txt to 10.1.1.2
2025-09-26T16:25:12.703331+01:00 pi-master dnsmasq-tftp[5629]: sent /tftpboot/8b07bcc6/cmdline.txt to 10.1.1.2
2025-09-26T16:25:12.894988+01:00 pi-master dnsmasq-tftp[5629]: file /tftpboot/8b07bcc6/armstub8.bin not found for 10.1.1.2
2025-09-26T16:25:12.897311+01:00 pi-master dnsmasq-tftp[5629]: error 0 Early terminate received from 10.1.1.2
2025-09-26T16:25:12.897634+01:00 pi-master dnsmasq-tftp[5629]: failed sending /tftpboot/8b07bcc6/kernel8.img to 10.1.1.2
2025-09-26T16:25:20.351041+01:00 pi-master dnsmasq-tftp[5629]: sent /tftpboot/8b07bcc6/kernel8.img to 10.1.1.2
2025-09-26T16:25:30.883634+01:00 pi-master dnsmasq-dhcp[5629]: DHCPDISCOVER(eth0) b8:27:eb:07:bc:c6 
2025-09-26T16:25:30.884036+01:00 pi-master dnsmasq-dhcp[5629]: DHCPOFFER(eth0) 10.1.1.2 b8:27:eb:07:bc:c6 
2025-09-26T16:25:30.890479+01:00 pi-master dnsmasq-dhcp[5629]: DHCPREQUEST(eth0) 10.1.1.2 b8:27:eb:07:bc:c6 
2025-09-26T16:25:30.890877+01:00 pi-master dnsmasq-dhcp[5629]: DHCPACK(eth0) 10.1.1.2 b8:27:eb:07:bc:c6 
2025-09-26T16:25:31.501353+01:00 pi-master dnsmasq-dhcp[5629]: DHCPDISCOVER(eth0) b8:27:eb:07:bc:c6 
2025-09-26T16:25:31.501723+01:00 pi-master dnsmasq-dhcp[5629]: DHCPOFFER(eth0) 10.1.1.2 b8:27:eb:07:bc:c6 
2025-09-26T16:25:31.503320+01:00 pi-master dnsmasq-dhcp[5629]: DHCPREQUEST(eth0) 10.1.1.2 b8:27:eb:07:bc:c6 
2025-09-26T16:25:31.503641+01:00 pi-master dnsmasq-dhcp[5629]: DHCPACK(eth0) 10.1.1.2 b8:27:eb:07:bc:c6 
2025-09-26T16:25:31.559888+01:00 pi-master rpc.mountd[607]: authenticated mount request from 10.1.1.2:931 for /nfsroot/worker-3 (/nfsroot)

```
