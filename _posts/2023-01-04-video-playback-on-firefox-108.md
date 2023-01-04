# Video playback issues on non-snap firefox 108

Due to preference (read: prejudice), I have [disabled snapd](https://ubuntuhandbook.org/index.php/2022/04/remove-snap-block-ubuntu-2204/) and [installed firefox from PPA](https://www.omgubuntu.co.uk/2022/04/how-to-install-firefox-deb-apt-ubuntu-22-04) instead.

My workstation setup:

* [MSI Stealth 15M](https://www.msi.com/Laptop/Stealth-15M-A11SX-GTX/Specification)
  * 11th gen Intel Core i7
  * **NVIDIA GeForce GTX 1660**
* Ubuntu Jammy 22.04 Desktop
* **Wayland** windows server
* Secure boot enabled in BIOS
* Proprietary [**nvidia-driver-525**](https://www.cyberciti.biz/faq/ubuntu-linux-install-nvidia-driver-latest-proprietary-driver/)

## Problem description

Launch firefox from commandline, the follow non-fatal error shows up. Interestingly, none of these issues show up in Chrome browser.

```text
[GFX1-]: No GPUs detected via PCI
[GFX1-]: glxtest: process failed (received signal 11)
```

1. <https://webglsamples.org/aquarium/aquarium.html>

    ```text
    It does not appear your computer supports WebGL.
    Click here for more information.

    Status: WebGL creation failed: * WebglAllowWindowsNativeGl:false restricts context creation on this system. () * Exhausted GL driver options. (FEATURE_FAILURE_WEBGL_EXHAUSTED_DRIVERS)
    ```

2. <https://webglreport.com/>

    ```text
    This browser supports WebGL 1, but it is disabled or unavailable.

    This browser supports WebGL 2, but it is disabled or unavailable.
    ```

3. Many youtube videos play just fine but not this one, <https://www.youtube.com/watch?v=69CRnH3lbDE>. And all Facebook, TikTok videos won't play in firefox.

    ```text
    An error occurred. Please try again later. (Playback ID: 1Svca1VMbs2Y016)
    ```

    Meanwhile, firefox logs these errors:

    ```text
    [2023-01-04T09:54:36Z ERROR mp4parse] Found 2 nul bytes in "\0\0"
    [Child 52422, MediaDecoderStateMachine #1] WARNING: Decoder=7f733d432400 Decode error: NS_ERROR_DOM_MEDIA_FATAL_ERR (0x806e0005) - Error no decoder found for audio/mp4a-latm: file /build/firefox-5bxMr3/firefox-108.0.1+build1/dom/media/MediaDecoderStateMachineBase.cpp:151
    [2023-01-04T09:54:36Z ERROR mp4parse] Found 2 nul bytes in "\0\0"
    [Child 52422, MediaDecoderStateMachine #1] WARNING: Decoder=7f733d433000 Decode error: NS_ERROR_DOM_MEDIA_FATAL_ERR (0x806e0005) - Error no decoder found for audio/mp4a-latm: file /build/firefox-5bxMr3/firefox-108.0.1+build1/dom/media/MediaDecoderStateMachineBase.cpp:151
    [2023-01-04T09:54:36Z ERROR mp4parse] Found 2 nul bytes in "\0\0"
    [Child 52422, MediaDecoderStateMachine #1] WARNING: Decoder=7f733d433600 Decode error: NS_ERROR_DOM_MEDIA_FATAL_ERR (0x806e0005) - Error no decoder found for audio/mp4a-latm: file /build/firefox-5bxMr3/firefox-108.0.1+build1/dom/media/MediaDecoderStateMachineBase.cpp:151
    [Child 52422, MediaDecoderStateMachine #1] WARNING: Decoder=7f733f4b8700 Decode error: NS_ERROR_DOM_MEDIA_FATAL_ERR (0x806e0005) - Error no decoder found for video/avc: file /build/firefox-5bxMr3/firefox-108.0.1+build1/dom/media/MediaDecoderStateMachineBase.cpp:151
    [Child 52422, MediaDecoderStateMachine #1] WARNING: Decoder=7f733fa6d600 Decode error: NS_ERROR_DOM_MEDIA_FATAL_ERR (0x806e0005) - Error no decoder found for video/avc: file /build/firefox-5bxMr3/firefox-108.0.1+build1/dom/media/MediaDecoderStateMachineBase.cpp:151
    ```

## Solution

The first two issues are the same, *probably* due to a bug in Nvidia driver and can be resolved by forcing firefox to use webgl.

From <https://support.biodigital.com/hc/en-us/articles/218322977-How-to-turn-on-WebGL-in-my-browser>

1. Go to about:config in your address bar.
2. Search for webgl.force-enabled and make sure this preference is set to true. If it is currently set to false, click the toggle icon on the far right to change the value to true.
3. Search for webgl.disabled and make sure this preference is set to false. If it is currently set to true, click the toggle icon on the far right to change the value to false.
4. Restart Firefox to apply your new settings.

The third issue is due to missing decoder.

```bash
sudo apt -y install ffmpeg
```
