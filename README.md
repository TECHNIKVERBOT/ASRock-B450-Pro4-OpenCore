# ASRock-B450-Pro4-OpenCore
 
### Before you give this EFI a try, make sure you read [this](#set-cpu-core-count), [this](#boot-arguments-for-different-configurations) and [this](#generating-your-own-serial-and-editing-rom)!

This repo includes an OpenCore EFI for the ASRock B450 Pro4 motherboard.

Testing on:

Model | ASRock B450 Pro4
------------- | ---------------
CPU | AMD Ryzen 7 3700X
GPU | AMD Radeon RX 6600
RAM | 16 GB DDR4-3000 (G-Skill Aegis)
Disk (SATA) | Intenso High Performance (Windows 11)
Disk (SATA) | Intenso High Performance (macOS)
macOS | Ventura Beta 5

## What works?

- Audio
- Boot
- USB
- Ethernet
- GPU acceleration
- Sleep
- [Power Management](#AMD-CPU-Power-Management)

## What doesn't work?

- back audio output
- microphone output
- intermittent crashes during boot

## Set CPU core count

Core Count patch needs to be modified to boot your system, if not using a 6 core CPU. Find the three `algrey - Force cpuid_cores_per_package` patches and alter the `Replace` value only.

Changing `B8000000 0000`/`BA000000 0000`/`BA000000 0090`* to `B8 <CoreCount> 0000 0000`/`BA <CoreCount> 0000 0000`/`BA <CoreCount> 0000 0090`* substituting `<CoreCount>` with the hexadecimal value matching your physical core count.

See the table below for the values matching your CPU Core Count.

| CoreCount | Hexadecimal |
|--------|---------|
|   4 Core  | `04` |
|   6 Core  | `06` |
|   8 Core  | `08` |
|   12 Core | `0C` |
|   16 Core | `10` |

  So for example on an 8 Core CPU like the 3600 the `Replace` value would result in these replace values, `B8 08 0000 0000`/`BA 08 0000 0000`/`BA 08 0000 0090` (default configuration in this EFI)

## BIOS settings

[Dortania BIOS Guide](https://dortania.github.io/OpenCore-Install-Guide/AMD/zen.html#amd-bios-settings)

## How to install

Download this repo and place the EFI folder into your internal drive's EFI partition... That's it.

## How to Install macOS:

There are two ways you can make a USB installer:

1. Have a working install of macOS, download the Installer from the App Store, then make a bootable Installer with the `createinstallmedia` command

2. If you are using Windows, use [macrecovery.py](https://dortania.github.io/OpenCore-Install-Guide/installer-guide/winblows-install.html) from the offical OpenCore release package.

After you have created a bootable Installer, copy the EFI folder to the EFI partition of the installer drive and install as usual (GUID Partiton Map; APFS). After the installation, mount the EFI partition of the installed OS and copy the EFI folder to its partition.

## Boot arguments for different configurations

If you're using an AMD Navi GPU, you won't need to change anything in the `boot-args`

For AMD Polaris and NVIDIA Kepler, remove `agdpmod=pikera` from `boot-args`

For NVIDIA Maxwell and Pascal, remove `agdpmod=pikera` and add `nvda_drv_vrl=1` for enabling web drivers.

For AMD GCN, remove `agdpmod=pikera` and add `radpg=15` and `-raddvi`.

## Generating your own serial and Editing ROM

Use GenSMBIOS (https://github.com/corpnewt/GenSMBIOS) to generate a serial for one of the following SMBIOS:

| SMBIOS | Usage | Notes |
| -------- | --------- | --------- |
| MacPro7,1 | AMD Polaris and newer | MacPro7,1 is exclusive to macOS 10.15 Catalina and newer |
iMacPro1,1 | NVIDIA Kepler / Maxwell / Pascal or AMD Polaris and newer | Use if you need High Sierra or Mojave, otherwise use MacPro7,1 |
iMac14,2: | NVIDIA Kepler / Maxwell / Pascal | Use if you get black screens on iMacPro1,1 using an NVIDIA Kepler GPU or after installing Web Drivers |
MacPro6,1 | AMD GCN GPUs (supported HD 7000/8000 + R5/R7/R9 series) |

use Xcode, ProperTree or any decent plist editor to manually enter the details in the following sections of the config (as shown in the video): (SystemProductName, SystemSerialNumber, MLB, and UUID)

https://user-images.githubusercontent.com/59102649/116117179-3ea51200-a6bc-11eb-8a18-a03f7bb5bf1d.mp4

You should also put in your ethernet adapter's MAC address into the ROM section.

## AMD CPU Power Management

While macOS might not officially support AMD CPU Power management, there are community efforts to add it. Specifically being `SMCAMDProcessor.kext` and `AMDRyzenCPUPowerManagement.kext`, both of which are included in the EFI.

Warning: They're known to create stability issues as well, if you're receiving random kernel panics or issues booting, do keep in mind these kexts may be the culprit, hence why tey're disabled by default.

## Credits

* [AMD-OSX](https://github.com/AMD-OSX/AMD_Vanilla) (for the kernel patches)
* [acidanthera](https://github.com/acidanthera) (for OpenCore and the kexts)
* [dortania](https://dortania.github.io/OpenCore-Install-Guide/) (for their awesome guide)
* [aluveitie](https://github.com/aluveitie/RadeonSensor) (for the Radeon GPU temperature kexts)
