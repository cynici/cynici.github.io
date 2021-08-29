# Replace stock recovery image on POCO F1 with TWRP using Magisk

I decided to replace MIUI 12.0.3 on my POCO F1 with a custom ROM because the phone will get no further update from Xiaomi.

At this point, I have unlocked the boot loader on my POCO F1 running MIUI 12.0.3. The next step is to replace the stock recovery image with
the venerable [Magisk](https://github.com/topjohnwu/Magisk) android application. The reason for using Magisk is to replace the recovery image
on the phone permanently. Otherwise, MIUI will replace TWRP with the stock
recovery image every time the phone reboots, in which case, I will have to
re-run steps below to boot into TWRP image.

Watch the video to get an idea the entire procedure using MS Windows, <https://www.youtube.com/watch?v=ZKEHRco06Eo> Below I describe what I had to do differently using Fedora 34 Workstation.

Also important as emphasiseed in the video is that I have decided to retain "Encryption and Credentials" encrypted on the phone.

1. Download the latest Magisk APK from <https://github.com/topjohnwu/Magisk/releases> and **save the APK file on the phone**

1. Download the latest Android platform tools from <https://developer.android.com/studio/releases/platform-tools>

1. Unzip the package anywhere, e.g. /tmp/platform-tools

1. Download the latest TWRP specific to the phone from <https://twrp.me/xiaomi/xiaomipocophonef1.html> I was actually confused by the instructions given on the page. In the end, what really mattered was to download the image file and do nothing else. At this time, the file was `twrp-3.5.2_9-0-beryllium.img`

1. Copy the image file to /tmp/platform-tools and rename it as `twrp.img` for convenience, and also copy it to the phone.

1. On the phone, go to Developer Options to enable USB debugging

1. Connect the phone to the computer using the original (or fully USB2 compliant) cable

1. Test connectivity between them. The output should display one device detected. If not, try other USB ports on the computer. Avoid USB-3 ports if possible because there's reported issues involving USB Hub.

    ```sh
    cd /tmp/platform-tools
    sudo ./adb devices
    ```

1. Use adb to reboot to "fastboot" mode. After the command, the phone should reboot to a static "fastboot" image.

    ```sh
    sudo ./adb reboot bootloader
    ```

1. Test connectivity again to be sure but using `fastboot` command this time. Again, the phone should be listed.

    ```sh
    sudo ./fastboot devices
    ```

1. Boot to TWRP image. When TWRP main menu shows up on the phone, disconnect the USB cable. From here on, follow every step exactly given in the video which I have listed below for convenience.

    ```sh
    sudo ./fastboot boot twrp.img
    ```

1. On TWRP main menu, select "Install image" and browse to the TWRP image file previously copied to the phone, and select "Recovery". When complete,
select Home option to return to main TWRP menu, select Reboot - Recovery

1. Rebooting back to TWRP main menu, select Install and browse to Magisk APK file previously copied to the phone. Reason for doing this step is well explained in the video. When done, Reboot System.

1. Test that TWRP has replaced the stock recovery image successfully, switch off the phone. Press Volume-up and Power button at the same time to go into recovery mode. It should show TWRP main menu now.
