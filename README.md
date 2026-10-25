# libfprint-goodix-5125-git (Arch Linux / CachyOS PKGBUILD)

Arch Linux / CachyOS package for `libfprint` with native driver support for the **Goodix USB 27c6:5125** fingerprint reader (commonly found on Huawei MateBook and Honor MagicBook laptops).

## Prerequisites

Install the build tools and fingerprint daemon:

```bash
sudo pacman -S --needed base-devel git meson ninja gobject-introspection glib2-devel fprintd
```

## Building and Installing Locally

From this directory:

```bash
makepkg -si
```

This compiles:
1. The `ChicagoHS` native biometric matching engine (`libgoodix-chicago-native.a`)
2. `libfprint` with `goodix_27c6_5125` driver enabled alongside all standard upstream drivers
3. Installs `libfprint-2.so` and the requisite udev/hwdb rules to `/usr/lib/`

## Setting Up and Enrolling Fingerprints

1. Enable and start the fingerprint service:
   ```bash
   sudo systemctl enable --now fprintd.service
   ```

2. Verify that `fprintd` detects the device:
   ```bash
   fprintd-list "$USER"
   ```

3. Enroll your finger (e.g. right index finger):
   ```bash
   fprintd-enroll -f right-index-finger
   ```
   *(Touch the sensor repeatedly following the prompts until enrollment completes)*

4. Verify your enrolled fingerprint:
   ```bash
   fprintd-verify
   ```

5. Enable PAM authentication (for sudo, SDDM, GDM, or Polkit):
   - For `sudo`, edit `/etc/pam.d/sudo` and add at the top:
     ```pam
     auth sufficient pam_fprintd.so
     ```
   - For SDDM / Plasma / GDM, see the Arch Wiki guide: https://wiki.archlinux.org/title/Fprint

## Disclaimer & Disclosure

* **Personal Use:** This package was created and is maintained primarily for personal use.
* **Tested on Real Hardware:** The package has been built, installed, and verified on a **Huawei MateBook X Pro (MACHD-WXX9)** running Arch Linux / CachyOS equipped with the Goodix USB `27c6:5125` sensor.
* **No Warranty:** This repository is provided "as-is", without warranty of any kind, express or implied. Use it at your own risk.
* **AI Attribution:** The `PKGBUILD` packaging recipe, build automation, and documentation were created with the assistance of an AI coding agent (**Google DeepMind's Antigravity / Gemini**).
* **Upstream Credits:** Driver development and reverse-engineering credits belong to [Berke Kabagöz and Guido Soranzio (`sorguido/goodix-27c6-5125`)](https://github.com/sorguido/goodix-27c6-5125).

