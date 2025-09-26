# pi-network-boot
Network booting Raspberry Pi's

## Overview

This covers network booting Raspberry Pi 3 Model B Plus Rev 1.3 without using a micro SD card on the workers.

```
$ grep Model /proc/cpuinfo 

Model		: Raspberry Pi 3 Model B Plus Rev 1.3
```

## Networking

```

                        +---------------------+
                        |     S W I T C H     |
                        +---------------------+





```
## Prerequisites

### Update OTP on Pi 3 models

The OTP (One Time Programmable) Memory of the Pi 3 model needs updating before you can boot over the network. To do this you will need to download and install the latest version of Raspberry Pi OS from https://www.raspberrypi.com/software/operating-systems/ on an micro SD card. It is recommended to use the Pi OS Lite.

For this we used the version from 13th May 2025 which is based on Bookworm: 
https://downloads.raspberrypi.com/raspios_lite_arm64/images/raspios_lite_arm64-2025-05-13/2025-05-13-raspios-bookworm-arm64-lite.img.xz

Once you have booted open a terminal and run `echo program_usb_boot_mode=1 | sudo tee -a /boot/config.txt`

e.g.
```
echo program_usb_boot_mode=1 | sudo tee -a /boot/config.txt
```

Next you need to reboot to get it to read the value from the config file.

To check it worked, open a terminal and run `vcgencmd otp_dump | grep 17:` to check the OTP value. This should be `17:3020000a`

e.g.
```
$ vcgencmd otp_dump | grep 17:
17:3020000a
```


