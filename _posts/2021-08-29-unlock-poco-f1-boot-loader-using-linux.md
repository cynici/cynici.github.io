# Unlock Xiaomi POCO F1 bootloader using Linux

I decided to replace MIUI 12.0.3 on my POCO F1 with a custom ROM because the phone will get no further update from Xiaomi. I have written the entire procedure in three parts:

- Unlock bootloader
- [Replace stock recovery image with TWRP using Magisk](/2021/08/29/replace-stock-recovery-with-twrp-using-magisk.html)
- [Install (or flash) custom ROM using TWRP](/2021/08/29/install-custom-rom-using-twrp.html)

## Possible consequences after unlocking

If in future, Google Safety enforces stricter checks, it may flag the phone as insecure. Any application which relies on the Google Safety status may cease to function, see <https://news.ycombinator.com/item?id=25025340>

## Prerequisites

- Register on [Xiaomi website](https://account.xiaomi.com/) to get an account
- Log in to the account on the phone
- Insert a valid SIM card with sufficient internet data bundle, at least 50 MB required during the unlock process

## How

The is part one where I resort to using the unofficial XiaoMiToolV2 because I use Fedora 34 Workstation instead of MS Windows which the official unlock app runs on.

1. As at 2021-08-29, there is no form to fill to request for unlock approval from Xiaomi or any waiting period. Instead, follow every step described in this video <https://youtu.be/pByHHTvms4k>

1. Clone XiaoMiToolV2 repo and check out linux branch

    ```sh
    git clone https://github.com/francescotescari/XiaoMiToolV2.git && cd XiaoMiToolV2 && git checkout linux
    ```

1. Edit one Java source file exactly as described <https://github.com/francescotescari/XiaoMiToolV2/issues/23#issuecomment-904082515>

1. Install openjdk-11, `sudo dnf install java-11-openjdk`

1. Install gradle by following just Step 2, https://docs.gradle.org/current/userguide/installation.html#step_2_unpack_the_distribution. I didn't bother to set up PATH, etc. described further on the page.

1. Set up a new Gradle wrapper. This step inevitably overwrites a few Gradle-related files in the repo including gradlew

    ```sh
    # Step above got me gradle-7.2. Adjust this if you've got a newer release.
    /opt/gradle/gradle-7.2/bin/gradle wrapper
    ```

1. Build using the wrapper, `./gradlew build`. If this fails, you have probably messed up the Java source code edit.

1. Run application, sudo ./gradlew run

After clicking on the "unlock bootloader" button in the app, please be patient. My phone showed a static "MIUI" for over 3 minutes which kinda frightened me. But it eventually booted normally into a fresh install (like factory reset) and showed the "unlocked" status under Developer Options.
