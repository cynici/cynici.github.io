# Slow screen unlock on Linux

I started using Ubuntu Jammy 22.04 desktop on [MSI Stealth 15M](https://www.msi.com/Laptop/Stealth-15M-A11SX-GTX/Specification) which comes with the following:

* 11th gen Intel Core i7
* NVIDIA GeForce GTX 1660

## My setup

* Secure boot enabled in BIOS
* [nvidia-driver-525](https://www.cyberciti.biz/faq/ubuntu-linux-install-nvidia-driver-latest-proprietary-driver/)
* standard Gnome Desktop with screen lock enabled

Everything worked fine except:

* After entering the correct password, screen unlock could take up to 15 seconds to restore my desktop session
* *nvidia-smi* does not work

    ```text
    NVIDIA-SMI has failed because it couldn't communicate with the NVIDIA driver. Make sure that the latest NVIDIA driver is installed and running.
    ```
## Solution

Turns out that both issues are related.

During a screen unlock session, I ran *journalctl* and noticed these suspicious lines:

```text
gdm-session-worker modprobe: ERROR: could not insert 'nvidia': Operation not permitted
```

With that, I found this crucial article, https://clay-atlas.com/us/blog/2022/07/29/solved-nvidia-smi-has-failed-because-it-couldnt-communicate-with-the-nvidia-driver-make-sure-that-the-latest-nvidia-driver-is-installed-and-running/


I didn't reinstall nvidia driver but merely carried out these steps:

1. `sudo mokutil --import /var/lib/shim-signed/mok/MOK.der`
2. Set a **8-character password** in the configure secure boot stage, and remember your password
3. If you enter the screen of Perform MOK management, please select `Enroll MOK > Continue > Yes`
4. Enter the password at the screen of Enroll the key(s)?
5. Select OK to reboot
6. After rebooting, use nvidia-smi command to check the driver is work.

    ```text
    nvidia-smi 
    Thu Dec  8 08:24:56 2022       
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 525.60.11    Driver Version: 525.60.11    CUDA Version: 12.0     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  NVIDIA GeForce ...  Off  | 00000000:01:00.0 Off |                  N/A |
    | N/A   48C    P8    12W /  N/A |    140MiB /  6144MiB |      0%      Default |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
                                                                                
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |    0   N/A  N/A      3430      G   /usr/lib/xorg/Xorg                 36MiB |
    |    0   N/A  N/A      4581    C+G   ...178791064225627906,131072      101MiB |
    +-----------------------------------------------------------------------------+
    ```

## ps

Coming from [MacBook Pro 2019 15-inch](https://support.apple.com/kb/SP794?locale=en_ZA), the most obvious deficiencies on 15M are:

* battery could barely last 4 hours on balanced mode
* fingerprint sensor not built-in
