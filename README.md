# libfprint-goodix-5125-git (AUR Package)

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

## Publishing to AUR

To publish this package to the Arch User Repository (AUR):

1. Clone your AUR repository once registered on https://aur.archlinux.org:
   ```bash
   git clone ssh://aur@aur.archlinux.org/libfprint-goodix-5125-git.git
   ```
2. Copy `PKGBUILD` and `.SRCINFO` into that repository.
3. Commit and push:
   ```bash
   git add PKGBUILD .SRCINFO
   git commit -m "Initial commit for libfprint-goodix-5125-git"
   git push origin master
   ```

## Disclaimer & AI Attribution

This packaging recipe (`PKGBUILD`), build automation, and setup documentation were created with the assistance of an AI coding agent (**Google DeepMind's Antigravity / Gemini**) and tested/verified on physical hardware running Arch Linux / CachyOS with a Goodix 27c6:5125 sensor.

* Upstream driver credits: [Berke Kabagöz and Guido Soranzio (`sorguido/goodix-27c6-5125`)](https://github.com/sorguido/goodix-27c6-5125).
* This repository is provided as-is without warranty.

