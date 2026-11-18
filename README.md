# Fedora Rolling (Niri Edition phOS)

An immutable Linux distribution (bootc/OSTree) based on **Fedora Atomic** and built using the **Universal Blue** image template.

---

## Key Features

### Base & Architecture
* **Base System:** Built on top of the official `quay.io/fedora-ostree-desktops/sway-atomic:45` image (Fedora 45 Atomic).
* **Atomic Updates:** Fully compatible with `bootc` and `rpm-ostree` for transactional, safe updates with instant rollback capabilities.
* **Enabled Repositories:** Native integration of **RPM Fusion** (Free & Non-Free) and system-wide **Flathub** pre-configured for desktop applications.

### Desktop Environment (Wayland)
* **Compositor:** **Niri** (scrollable-tiling Wayland compositor) paired with **Noctalia** for shell and desktop interface management.
* **X11 Compatibility:** Full support for legacy X11 applications via `xwayland-satellite` and `xorg-x11-server-Xwayland`.
* **Portals & Integration:** Complete setup of `xdg-desktop-portal` (GTK, GNOME, WLR) and `gnome-keyring` for credential management and screen sharing.
* **Session Cleanup:** Removal of redundant tray applets (`blueman`, `network-manager-applet`) and the default `niri.desktop` session file in favor of a custom session configuration.

### Development, Containers & Local AI
* **Docker Engine:** Full installation of official Docker CE packages (`docker-ce`, `containerd.io`, `buildx`, `compose-plugin`, `rootless-extras`).
* **Distrobox:** Pre-installed to easily create and run isolated, mutable Linux environments directly from the terminal.
* **AI & Hardware Acceleration:** Full Vulkan support (`mesa-vulkan-drivers`, `vulkan-tools`, `clinfo`) and integration of **`llama-cpp-vulkan-rpc`** (via the `fm-0p3n-d3v/llama-cpp-vulkan-rpc` COPR) to run local LLMs with GPU acceleration and RPC support.

### Networking & Connectivity
* **Wi-Fi Stack:** Complete replacement of `wpa_supplicant` (masked at the systemd level) with **`iwd`** (iNet Wireless Daemon) integrated into NetworkManager.
* **Firmware:** Includes updated firmware packages for Intel network and Wi-Fi cards (`iwlwifi-mvm`, `dvm`, `mld`, `iwlegacy`) alongside full Bluetooth support (`bluez`, `bluez-tools`).
* **Mesh VPN:** Pre-installed **NetBird** (`netbird` and `netbird-ui`) for zero-config peer-to-peer private networks powered by WireGuard.
* **Remote Access:** SSH daemon (`sshd.service`) enabled by default on system boot.

### Included Utilities & Tools
* **Terminal & Editor:** `alacritty`, `xed`.
* **System Monitoring:** `htop`, `nvtop` (GPU monitor), `fastfetch`, `smartmontools`.
* **Clipboard & Media:** `wl-clipboard`, `cliphist`, `mediawriter`, `fuse` / `fuse-libs` (for AppImage execution).

---

## Custom Configuration & Services

During the build process, the image integrates custom configuration files and scripts from the `system_files/` directory:
1. **Custom Executables:** All scripts placed in `/usr/bin/` are automatically granted execution permissions.
2. **System Services:** `.service` units located in `/usr/lib/systemd/system/` are globally enabled under `multi-user.target`.
3. **User Services:** `.service` units located in `/usr/lib/systemd/user/` are automatically symlinked to `graphical-session.target` to launch alongside the graphical session.

---

## Installation & Rebase

You can install this distribution by rebasing from any existing **Fedora Atomic** installation (such as *Fedora Silverblue*, *Fedora Kinoite*, *Fedora Sway Atomic*, or *Fedora Sericea*) or any **Universal Blue** image (such as *Bluefin* or *Bazzite*).

> **Why is a two-step rebase required?**
> This image is cryptographically signed using **Cosign**. On a fresh Fedora installation, your system does not yet have this repository's public signing key (`cosign.pub`) or container policy installed. 
> 1. The **first rebase** downloads the image and automatically installs the public signing key onto your system.
> 2. The **second rebase** switches your system to the verified channel, enforcing signature checks for all future updates.

---

### Method 1: Using `bootc` (Recommended for Fedora 41+)

1. **First Switch (Install image & fetch signing keys):**
   Open a terminal and run:
   ```bash
   sudo bootc switch ghcr.io/fm-0pend3v/phOS_niri:latest
   ```
2. **Reboot your system:**
   ```bash
   systemctl reboot
   ```
3. **Second Switch (Enforce Cosign signature verification):**
   Once booted into the new system, run:
   ```bash
   sudo bootc switch --enforce-container-sigpolicy ghcr.io/fm-0pend3v/phOS_niri:latest
   ```
4. **Final Reboot:**
   ```bash
   systemctl reboot
   ```

---

### Method 2: Using `rpm-ostree` (Alternative / Legacy)

1. **First Rebase (Unverified):**
   ```bash
   rpm-ostree rebase ostree-unverified-registry:ghcr.io/fm-0pend3v/phOS_niri:latest
   ```
2. **Reboot your system:**
   ```bash
   systemctl reboot
   ```
3. **Second Rebase (Cosign Signed):**
   ```bash
   rpm-ostree rebase ostree-image-signed:docker://ghcr.io/fm-0pend3v/phOS_niri:latest
   ```
4. **Final Reboot:**
   ```bash
   systemctl reboot
   ```

---

### Recommended Post-Install Setup

* **NetBird VPN:** Connect your machine to your mesh network:
  ```bash
  sudo netbird up
  ```
* **Flatpak Applications:** Flathub is pre-configured system-wide. You can install apps right away:
  ```bash
  flatpak install flathub <APPLICATION_ID>
  ```

---

## Credits
* [Universal Blue](https://universal-blue.org) for the build template and cloud-native desktop infrastructure.
* [Niri](https://github.com/niri-wm/niri) for the Wayland compositor.
* [Noctalia Shell](https://github.com/noctalia-dev/noctalia) for the Wayland desktop shell.
* [Fedora Project](https://fedoraproject.org) for the Fedora Atomic base.

---

## License

This project is licensed under the **Apache License 2.0** - see the [LICENSE](LICENSE) file for details.

* Built using the [Universal Blue](https://github.com/ublue-os/image-template) image template (Apache-2.0).
* Included third-party packages, firmware, and repositories (such as RPM Fusion, Docker CE, and NetBird) remain subject to their respective licenses.
