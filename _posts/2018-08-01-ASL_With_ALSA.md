---
layout: post
title: "AllStar, Alsa & Pulseaudio without OSS"
date: 2018-08-01 12:00:00
tags: 
 - "amateur radio"
 - repeater
 - n0pkt
 - "rasberry pi"
 - OSS
 - electronics
 - PulseAudio
 - Alsa
 - URIx
description: How to setup AllStar using Alsa & Pulseaudio to access the URIx instead of OSS!
---

## ASL with no OSS Kernel Module (since it's now marked as _broken_ and has been deprecated for _10_ years)

This is the process that I used to facilitate the _"emulation"_ of OSS `/dev/dsp1` using [ALSA](https://www.alsa-project.org/main/index.php/Main_Page), [PulseAudio](https://www.freedesktop.org/wiki/Software/PulseAudio/Documentation/User/Community/) and [OSSPD](https://github.com/libfuse/osspd).

Below you will find basic guidelines and what I edited in order to allow ASL to function with a _CURRENT_ kernel on raspbian.  This document makes a few assumptions:
1. You know how to use an editor, of your choice, in the shell of your choice.
2. You have ASL in a state where it will at least start up with the _CURRENT_ kernel.. _i.e. you built and installed the required dahdi modules from source_
3. You know how to install applications on whatever distribution that you happen to be running

Again, the good news here is that you can run current kernels without trudging through and trying to enable _broken_.. so that means security fixes and performance patches etc...  Just a little more admin overhead for now.

### Caveats

Before we dive into it to far, I want to make note of a few current caveats.  Firstly I have not found a way to make OSSPD work properly with pulse running as a system wide service (if you don't know what I'm talking about, I suggest RTFM or move on).  Secondly, I have not been able to successfully get Pulse to run as a daemon using systemctl, there are issues because when you do it this way pulse expects a window manager and we don't want that overhead on the system IMHO....  So what this means is that unfortunately at this time (I'm working on a solution) when a system reboots the user needs to ssh into the box and manually do a few things.

>If anyone gets pulse running correctly at startup, I'm all ears and will happily publish the info!
{: .note}

Yes, I did try fairly exhaustively to use AOSS and ALSA-OSPD but was never able to make it work, so if you are so inclined please feel free because if we can eliminate Pulse from the equation it would simplify things a bit.  Of course having pulse in there gives us a lot of other capabilities.. so there is a tradeoff I suppose.

### Basic Steps Outline
1. **OPTIONAL** Disable the onboard audio (for Pi 3 etc...) unless you have an explicit reason to have it..
 	* Edit /lib/modprobe.d/raspi-blacklist.conf and add `blacklist snd_bcm2835`
 	* reboot
2. Determine hardware location(s) (run all as root **or sudo** unless otherwise noted)
 	* lsusb (look for C-Media Electronics, Inc) to be sure it's even listed (your URI)
 	* aplay -l (make note of the card # associated with the USB PnP Sound Device)
3. Edit /usr/share/alsa/alsa.conf to make the URI (USB Device) the default
	* find defaults.ctl.card and defaults.pcm.card
 		- their default value is 0, change this to the # value identified in step 1 using the aplay -l command
5. Reboot
6. Install OSSPD
7. A few more edits
8. Service restarts
9. Audio should now work via alsa, pulse and aoss

### Slightly more detail

These are the exact steps that I took to emulate OSS.  Please remember to check logs files if you are experiencing things that you don't expect, they usually contain information that will help with the troubleshooting process.  

1. edit /etc/pulse/daemon.conf changing the following lines to read as noted
```
default-sample-rate = 48000
default-fragments = 5
default-fragment-size-msec = 2
```

2. edit /usr/share/alsa/alsa.conf changing the following lines to read as noted
```
defaults.ctl.card 1 #card number from aplay -l
defaults.pcm.card 1 #card number from aplay -l
```

3. edit /etc/pulse/default.pa changing the following lines to read as noted
```
load-module module-alsa-source device=hw:1,0 #device number specified in alsa.conf from aplay -l
load-module module-udev-detect tsched=0 ignore_dB=1
```

4. reboot

5. install osspd and execute the following after install
```
sudo systemctl stop osspd.service
```

6. edit /lib/systemd/system/osspd.service and change the ExecStart line as noted
```
ExecStart=/usr/sbin/osspd -f --dsp-slave=/usr/lib/osspd/ossp-padsp --adsp=dsp1
```

7. execute the following commands to restart services etc...
```
sudo systemctl daemon-reload
sudo pulseaudio --start
sudo systemctl start osspd.service
sudo systemctl restart asterisk.service
```

### To-Do
My short list of outstanding things... and in no specific order
1. Systemize pulseaudio
2. Benchtest audio levels of this setup vs OSS on the same hardware
3. Benchtest CTCSS output of this setup vs OSS on the same hardware (it all sounds super clean but I need science)
4. Produce some more examples of using pulse to pipe the audio stream out to other sources for recording/monitoring etc etc... (this gives us so many more capabilities than OSS ever dreamed of)

### Some additional resources:
[PulseAudio Troubleshooting](https://wiki.archlinux.org/index.php/PulseAudio/Troubleshooting)
[OSSPD on Github](https://github.com/libfuse/osspd)
[ALSA Home](https://www.alsa-project.org/main/index.php/Main_Page)