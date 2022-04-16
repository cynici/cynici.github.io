# Unlock Redmi Note 10 Pro and flash with custom ROM using Linux

I have decided to replace MIUI 12 on my Redmi Note 10 Pro (RN10pro) with Evolution X (evox) custom ROM.

This article describes what I have done (or wish I had :man_facepalming:).

Potential risk: if in future, Google Safety enforces stricter checks, it may flag any phone with unlocked bootloader as insecure. In which case, any application which relies on the Google Safety status may cease to function, see <https://news.ycombinator.com/item?id=25025340>

## Prerequisites

### Unlock bootloader

Unlike Poco F1, petty Xiaomi actually made me wait exactly 7 days to the minute to approve my bootloader unlock request.

I resorted to using the fabulous unofficial XiaMiToolV2 (XMTV2) because I only have a Linux Fedora 34 workstation to work on and Xiaomi only support Windows and MacOS.

In other words, to unlock bootloader on RN10pro, I had to run XMTV2 twice. The first time to submit request and the second time to complete the unlock process.

#### Submit unlock bootloader request

- Register on [Xiaomi website](https://account.xiaomi.com/) to get an account
- Log in to the account on the phone
- Insert a valid SIM card with sufficient internet data bundle, at least 50 MB required during the unlock process
- Run XMTV2 on Linux as described in </2021/08/29/unlock-poco-f1-boot-loader-using-linux.html>
- If successful, XMTV2 will terminate with this message:

> Failed to unlock your device, Xiaomi server returned error 20036:
> Error description: Your account has been bound to this device for not enough time
> You have to wait 7 days and 0 hours before you can unlock this device
> Server description: Please unlock 168 hours later. And do not add your account in MIUI again, otherwise you will wait from scratch

#### Complete unlock process 7 days later

Prerequisite:

- internet connectivity
- the same Mi account password

The [semi official unlocking guide](https://miui.blog/any-devices/bootloader-unlocking-guide/) answers a couple of questions I had:

- Does OTA update relock the bootloader? No
- Does flashing MIUI ROM relock the bootloader? No

There's always a "but". the OTA update answer above only applies if the stock recovery image has
not been replaced with TWRP. Otherwise, <https://www.reddit.com/r/Xiaomi/comments/jegjjk/can_i_do_ota_updates_after_rooting/>

Unlocking bootloader performs factory reset implicitly - all data on the phone will be wiped clean. So, back up everything e.g.

- Copy files, videos, downloads, etc. to external storage e.g. microSD card
- Sync contacts, SMS, etc. to MiCloud
- Sync camera photos to Google Drive
- In WhatsApp - Settings - Chats, manualy run "Backup chats"

When running XMTV2 the second time, it does not terminate with the earlier message. This time the process proceeds to rebooting normally from the FastBoot mode (recovery image) the phone was in. MIUI logo will remain static for over 2 minutes. DO NOT PANIC, this is normal.

You will be prompted to enter your Mi account password to complete the fresh setup of MIUI on the phone which requires connecting to the Internet.

### Download required packages

- Download Magisk, <https://github.com/topjohnwu/Magisk/releases>

- Download unofficial TWRP, <https://forum.xda-developers.com/t/recovery-unofficial-teamwin-recovery-project.4248449/>

- Download Evolution X unofficial for Redmi Note 10 Pro (sweet), <https://forum.xda-developers.com/t/rom-unofficial-evolution-x-5-8-redmi-note-10-pro-pro-max-sweet-sweetin.4293653/>

- Unlock bootloader
- [Replace stock recovery image with TWRP using Magisk](/2021/08/29/replace-stock-recovery-with-twrp-using-magisk.html)
- [Install (or flash) custom ROM using TWRP](/2021/08/29/install-custom-rom-using-twrp.html)

### Full system backup

From <https://www.getdroidtips.com/step-step-guide-backup-data-android-device/>

- Enable USB debugging
- Now connect your phone to the PC using the USB Cable
- Agree the USB Debugging prompt on your phone.
- Install latest release of Android SDK
- Back up app’s data and system data, `adb backup –all`

### Replace MIUI stock recovery image with TWRP

<https://www.getdroidtips.com/twrp-recovery-redmi-note-10-pro/>

### Replace MIUI with AOSP 11 ROM

<https://www.getdroidtips.com/android-11-redmi-note-10-pro/>
