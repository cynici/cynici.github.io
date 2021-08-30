# Install Evolution-X custom ROM on Xiaomi POCO F1

I decided to replace MIUI 12.0.3 on my POCO F1 with a custom ROM because the phone will get no further update from Xiaomi. I have the written entire procedure in three parts:

- [Unlock bootloader](/2021/08/29/unlock-poco-f1-boot-loader-using-linux.html)
- [Replace stock recovery image with TWRP using Magisk](/2021/08/29/replace-stock-recovery-with-twrp-using-magisk.html)
- Install (or flash) custom ROM using TWRP


## My shortlist of custom ROMs based on Android 10 or later

Having spent some days following discussions on Reddit and asking around, my choices of custom ROM boils down to these:

- Pixel Experience (pe10) or (pe11)
- Evolutoin-X (evox)
- LineageOS

## Getting started

For a start, watch this video before doing anything, <https://www.youtube.com/watch?v=UY6DBJ7_Wyg>

The procedure below should work just fine if I have a second thought about my choice. From the video, I have decided to install the latest released
by developer Lakshay Garg (@planckton), <https://t.me/PocoPhoneGlobalUpdates/6791> on account that he is the developer for the very received Pixel Experience. **Note that evox-5.8 does not cater OTA update.**

1. Download 1.7 GB ZIP from <https://sourceforge.net/projects/evolutionx-beryllium/files/EvolutionX_5.8_beryllium-11-20210622-0849-UNOFFICIAL.zip/download> and save it on the phone

1. Back up every you need because the phone will be wiped completely and reformatted.

1. Disable finger print, PIN, pattern, etc. security measures

1. Remove Mi and Google accounts

1. Reboot phone

1. Power off phone

1. Press Volume-up and Power button simulteneously to boot into recovery mode. Due to an [earlier procedure](2021/08/29/replace-stock-recovery-with-twrp-using-magisk.html), it should boot into TWRP main menu instead of the stock recovery menu.

1. Select "Wipe" and select Dalvik, Cache, System, Vendor and Data checkboxes. When wipe is complete, tap Home icon at the bottom of the screen to return to main menu.

1. Select "Install" and browse to the ZIP file. Swipe to confirm flash. **Deselect "reboot when complete"**. It takes about 2 minutes to complete.
Tab on Home icon to return to main menu.

1. Select "Wipe" - "Format data". 

1. Reboot - system. The first evox boot may take about 2 minutes.



