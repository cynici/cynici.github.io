# Touchpad intermittent malfunction on on Linux

The touchpad on my new laptop running Linux 5.8.14-1 [openSUSE Tumbleweed](https://software.opensuse.org/distributions/tumbleweed)
20201009 failed to work intermittently. The symptom showed up in the graphical login screen; 
the pointer won't respond to the touchpad and appeared stuck in the middle on the screen.

`dmesg` output would show this error:

```
[2.984796] i2c_hid i2c-UNIW0001:00: HID over i2c has not been provided an Int IRQ
[2.984864] i2c_hid: probe of i2c-UNIW0001:00 failed with error -22
```

When this happened, I had to reboot repeatedly until I could move the pointer on the login screen using the touchpad.

## Laptop hardware

[WootBook Metal II PF4NU1F AMD Ryzen 7 4800H 2.9GHz Octa Core 14" Full HD (1920x1080) IPS Space Black Laptop](https://www.wootware.co.za/wootbook-metal-ii-pf4nu1f-amd-ryzen-7-4800h-2-9ghz-octa-core-14-full-hd-1920x1080-ips-space-black-laptop.html)
rebranded from Tongfang PF4NU1F made by a company in China.

This hardware is also sold with different names in other markets, 
<https://www.reddit.com/r/AMDLaptops/comments/hzlcjo/all_of_the_vendors_that_are_offering_the_tongfang/>
E.g.:
- Eluktronics	THINN-15
- Schenker VIA 15 Pro
- TUXEDO Pulse 14"/15"
- PCZ 14" Fusion Pro
- Juno Computer (Linux)	Aurora 15
- Slimbook (Linux)	Slimbook Pro X 14"/15" (AMD)
- Slimbook (Linux)	KDE Slimbook 14"/15"
- LaptopwithLinux	PF4NU1F (14") / PF5NU1G (15")
- Skikk	15GRR1
- Laptopparts4less	Tongfang PF4NU1F (14") / PF5NU1G (15")
- RaionTech	RaionBook UB1R (14")
- Mechrevo	Mechrevo S2 Air (14")
- Mechrevo	Mechrevo Code 01 (15")
- Monsterlabs/Hansung	TFX44-0H (14")
- Monsterlabs/Hansung	TFX54-0H (15")
- Illegear	Ionic 15 Ryzen
- Commandos	Helium 5 Ryzen

## Solution

Thanks to Jeroen Jeurissen sharing [his solution](https://bugzilla.suse.com/show_bug.cgi?id=1177049#c4)
on [linux kernel bugzilla](https://bugzilla.kernel.org/show_bug.cgi?id=209413#c8)

I saved and compared `lsmod` output in both cases - fell back to terminal using Control+Alt+F2 hotkey 
when the touchpad malfunctioned. The kernel modules `hid_i2c` and `hid_generic` were absent 
when the touchpad didn't work.

Based on Jeroen's solution for Elan touchpad, I created `/etc/modprobe.d/99-touchpad.conf`

```
softdep i2c_hid pre: pinctrl_amd
softdep hid_generic pre: pinctrl_amd
```

Second attempt,

```
sudo rmmod i2c_hid
sudo modprobe i2c_hid
```

### 2020-10-25 update

So, my final solution on openSUSE Tumbleweed:

1. [Enable rc.local](https://www.linuxbabe.com/linux-server/how-to-enable-etcrc-local-with-systemd) 
1. Create /etc/rc.local 

  ```
  #! /usr/bin/env bash

  if lsmod | grep -q i2c_hid ; then
      echo "Touch pad detected."
  else
      rmmod i2c_hid
      modprobe i2c_hid
  fi
  ```
