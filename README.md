# ThinkPad T470 Hackintosh (OpenCore)

A comprehensive OpenCore EFI configuration and guide for running macOS on the Lenovo ThinkPad T470.

## 💻 Hardware Specifications

| Component | Details |
| :--- | :--- |
| **Laptop Model** | Lenovo ThinkPad T470 |
| **Processor** | Intel Core i5-6300U |
| **Integrated Graphics**| Intel HD Graphics 520 |
| **Audio Codec** | Realtek ALC298 (Layout-ID: 3) |
| **Wi-Fi & Bluetooth** | Intel Dual Band Wireless-AC 8265 (Driven by AirportItlwm) |
| **Ethernet** | Intel I219-LM Gigabit Ethernet (Driven by IntelMausi) |
| **SMBIOS Target** | MacBookPro13,1 |

## 🛠️ What's Working & Not Working

### 🟢 Working
* [x] **Graphics Acceleration:** Full QE/CI on Intel HD Graphics
* [x] **Audio:** Internal Speakers, Microphone, and Headphone Jack
* [x] **Power Management:** Proper CPU power states and Battery Status indicator
* [x] **Sleep / Wake:** Native sleep and wake configurations
* [x] **Keyboard & Trackpad:** Full gesture support (including TrackPoint) via VoodooI2C / VoodooPS2
* [x] **Connectivity:** Wi-Fi, Bluetooth, and Ethernet
* [x] **USB Ports:** All USB 3.0 ports mapped properly
* [x] **Camera:** Integrated 720p HD Webcam

### 🔴 Not Working / Work in Progress
* [ ]  **Power Management:** Have some issues with battery drain while in sleep mode
* [ ]  **Continuity:** Not being able to run AirDrop/Handoff due to lack of native Wifi/Bluetooth card in the configuration. Can fix this problem via using Broadcom cards.

---

## 🚀 Installation Guide

### 1. BIOS Settings
To ensure a successful boot and installation, your ThinkPad T470 BIOS must be configured with the following settings. Press `F1` at boot to enter BIOS:

* **Config:**
  * USB -> UEFI BIOS Support: `Enabled`
  * Power -> Intel SpeedStep technology: `Enabled`
  * CPU -> Intel Hyper-Threading Technology: `Enabled`
  * SATA Controller Mode: `AHCI`
* **Security:**
  * Security Chip: `Disabled` (or `Enabled` if you don't use Windows BitLocker)
  * Memory Protection -> Execution Prevention: `Enabled`
  * Virtualization -> Intel Virtualization Technology: `Enabled`
  * Virtualization -> VT-d: `Disabled` (You can keep it `Enabled` since `DisableIoMapper` is already active in `config.plist`)
  * Anti-Theft -> Computrace -> Current Setting: `Disabled`
  * Secure Boot -> Secure Boot: `Disabled`
* **Startup:**
  * UEFI/Legacy Boot: `UEFI Only`
  * CSM Support: `No`

---

### 2. Preparing the USB Installer
1. Download the macOS installer (Recommended: **macOS Monterey 12** or **Big Sur 11** for stable Skylake support).
2. Format your USB drive as GUID Partition Map (GPT).
3. Create the bootable USB installer using the official Apple `createinstallmedia` command via Terminal.
4. Mount the EFI partition of your USB drive and copy the entire `EFI` folder (containing `BOOT` and `OC`) into it.

---

### 3. Installation Steps
1. Insert the USB installer into the USB 3.0 port of your ThinkPad T470.
2. Power on the laptop and press `F12` repeatedly to open the Boot Menu.
3. Select your USB flash drive.
4. At the OpenCore picker menu, select **Install macOS**.
5. Once inside the macOS Recovery, open **Disk Utility**, format your target SSD drive as `APFS` with `GUID Partition Map`.
6. Close Disk Utility, select **Install macOS** and follow the on-screen instructions. The system will reboot several times; always select the macOS installer partition in the OpenCore picker until the setup wizard appears.

---

## 🛠️ Post-Installation

### 1. Setting Up Permanent Bootloader
After completing the macOS setup, the system can only boot with the USB plugged in. To make it boot independently:
1. Boot into macOS using your USB drive.
2. Download and open an EFI mounting tool (e.g., **MountEFI** or **OpenCore Configurator**).
3. Mount both the EFI partition of your USB drive and the EFI partition of your internal SSD.
4. Copy the entire `EFI` folder from your USB's EFI partition to your internal SSD's EFI partition.
5. Safely eject the USB drive and reboot.

### 2. Generating Unique SMBIOS (Crucial for iCloud & iMessage)
> ⚠️ **WARNING:** The `config.plist` in this repository uses `MacBookPro13,1`. You **MUST** generate your own unique serial numbers before connecting to the Internet or signing into Apple Services, otherwise your Apple ID could be blacklisted.

1. Download the [GenSMBIOS](https://github.com/corpnewt/GenSMBIOS) script.
2. Run `GenSMBIOS.command` (on macOS) or `GenSMBIOS.bat` (on Windows).
3. Select option `3` to "MacSerial" and download/update the latest database if prompted.
4. Select option `4` to "Generate SMBIOS".
5. Type `MacBookPro13,1` and press Enter.
6. Open your `config.plist` using an editor (like **ProperTree**), navigate to `PlatformInfo -> Generic`, and fill in the corresponding values:
   * **Type** ➔ `MacBookPro13,1`
   * **Serial** ➔ `SystemSerialNumber`
   * **Board Serial** ➔ `MLB`
   * **SmUUID** ➔ `SystemUUID`
7. Save the file.

### 3. Wi-Fi Configuration Note
This EFI includes `AirportItlwm.kext` tailored for native Intel Wi-Fi cards, allowing you to use the native macOS Wi-Fi menu. 
* **Note:** Ensure that the version of `AirportItlwm.kext` matches the specific macOS version you are running (e.g., Monterey version for macOS 12). If you upgrade or downgrade macOS, you must replace this specific kext file to prevent boot loops or kernel panics.
## 📚 Credits & Acknowledgments
* [Acidanthera](https://github.com/acidanthera) for OpenCore Bootloader and essential kexts.
* [Dortania](https://dortania.github.io/) for the ultimate OpenCore Desktop/Laptop Guide.
* The Hackintosh community and ThinkPad enthusiasts.
