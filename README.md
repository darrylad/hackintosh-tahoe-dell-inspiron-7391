# Setup

<img width="570" height="400" alt="image" src="https://github.com/user-attachments/assets/d4c862be-eabb-4840-b5ae-8e2a037c4e34" />


## Specs

[Dell Inspiron 7391 2-in-1](https://www.dell.com/support/manuals/en-hk/inspiron-13-7391-laptop/inspiron-7391-setup-and-specifications/specifications-of-inspiron-7391?guid=guid-7c9f07ce-626e-44ca-be3a-a1fb036413f9&lang=en-us)

- CPU: Intel Core i7-10510U
- GPU: Intel UHD Graphics 620
- Sound: Realtek ALC3204
- Network: Intel Wi-Fi 6 AX201 160MHz
- Storage: WD Blue SN5100 (Swapped with Intel H10 32 GB Optane Memory SSD) (AHCI mode)
- Bluetooth: Intel
- Display: 13.3" UHD IPS Touchscreen

## Functionality:

![Screenshot 2025-12-20 at 3 09 04 PM](https://github.com/user-attachments/assets/d201e490-916b-4549-8dd7-951aa926fcf0)

### Working:

- Audio (AppleALC with AppleHDA rollback - [reference](https://www.reddit.com/r/hackintosh/comments/1dffogz/audio_not_working/))
- WiFi (with itlwm and HeliPort; Doesn't connect to WPA3 Security WiFis)
- Sleep
- Trackpad gestures
- Battery read
- Touchscreen
- Bluetooth (Intel)
- GPU acceleration

### Not Working:

- 

## Procedure

### Tools used

- [OpCore Simplify](https://github.com/lzhoang2801/OpCore-Simplify) - to build the base EFI
- [OpenCorePkg](https://github.com/acidanthera/OpenCorePkg) - to download the macOS recovery image
- [USBToolBox](https://github.com/USBToolBox/tool) - for mapping ports and generating UTBMap.kext
- [OCAuxiliaryTools (OCAT)](https://github.com/ic005k/OCAuxiliaryTools) - for editing config.plist
- [Rufus](https://rufus.ie/en/) - to format flash drive
</p>

- [Hackintool](https://github.com/benbaker76/Hackintool) - to rebuild KextCache
- [OCLP-X](https://github.com/JeoJay127/OCLP-X) - for running root patches (especially AppleHDA rollback)

### Additional downloads

- [HoRNDIS.kext](https://drive.google.com/file/d/1oBKn5JwKisGOaADY5dE85-coVNqXrkFx/view) - for USB Tethering with Android devices

## Steps 

It is recommended that you follow these steps below instead of using my EFI directly. This process should work for most supported computers.

### TL;DR

1. Run OpCore-Simplify.bat and run Step 1 and Step 6.
2. Generate UTBMap.kext with USBToolBox, and add it and HoRNDIS.kext to `EFI\OC\Kexts` and update config.plist using OCAT.
3. Disable SIP (NVRAM > 7C436110-AB2A-4BBB-A880-FE41995C9F82 > csr-active-config = 03080000) and fix audio (DP > PciRoot(0x0)/Pci(0x1f,0x3) > device-id (Data) = C8340000 and layout-id (Data) = 22) in config.plist using OCAT. Save.
4. Download macOS recovery image in OpenCorePkg's `OpenCore-1.0.6-RELEASE\Utilities\macrecovery` using `python3 ./macrecovery.py -b Mac-CFF7D910A743CAAF -m 00000000000000000 -os latest download`.
5. Copy resulting com.apple.recovery.boot and EFI folder to a formatted flash drive.
6. Set flash drive as highest boot priority in BIOS, boot into OpenCore, and select the flash drive (dmg) to boot into macOS recovery.
</p>

7. In macOS recovery, use Disk Utility to format Tahoe partition as APFS, and install macOS onto it.
8. After installation, mount internal drive's EFI partition using OCAT (Cmd+M), and copy Boot and OC folder from flash drive's EFI partition to internal drive's EFI partition.
9. Run OCLP-X and run post install root patches.
10. Download RehabMan-CodecCommander-2018-1003.zip, and place the kext inside into `/Library/Extensions`.
11. Rebuild KextCache using Hackintool.
12. Approve loaded extension in Settings, reboot, and optionally reset NVRAM.
13. If OpenCore bootloader doesn't show up without flash drive, add boot option in BIOS with path `\EFI\OC\OpenCore.efi`.

### Detailed Steps

1. Boot into Windows.
2. Install Python from the Microsoft Store.
3. [Download the OpCore Simplify](https://github.com/lzhoang2801/OpCore-Simplify/archive/refs/heads/main.zip) reposiotry, extract, and run OpCore-Simplify.bat, and follow the instructions (Step 1 and Step 6).
4. Run [USBToolBox](https://github.com/USBToolBox/tool/releases/tag/0.2) (Windows.exe) and follow the instructions (Plug in a device into each usb port and wait for at least 5 seconds) and generate UTBMap.kext.
5. Open OCAuxilaryTools (OCAT), and open (Ctrl+O) `EFI\OC\config.plist`.
6. While OCAT is open, add the generated UTBMap.kext and the downloaded [HoRNDIS.kext](https://drive.google.com/file/d/1oBKn5JwKisGOaADY5dE85-coVNqXrkFx/view) to `EFI\OC\Kexts`. OCAT should automatically add these to the kernel extensions list.
7. To disable SIP, head to NVRAM tab > 7C436110... > csr-active-config, set its value to 03080000.
8. To fix audio, head to DP tab > PciRoot(0x0)/Pci(0x1f,0x3), set device-id (Data) to C8340000 and layout-id (Data) to 22.
9. Save and quit OCAT.
10. Extract [OpenCore-1.0.6-RELEASE.zip](https://github.com/acidanthera/OpenCorePkg/releases/tag/1.0.6) (OpenCorePkg), and go to `OpenCore-1.0.6-RELEASE\Utilities\macrecovery`, and open terminal in this directory (type cmd in the path bar).
11. In the cmd window, and run `python3 ./macrecovery.py -b Mac-CFF7D910A743CAAF -m 00000000000000000 -os latest download` to download macOS Tahoe's recovery image (cross-verify command for your desired version you chose in Step 1 of OpCore Simplify [in Dortania's Legacy macOS: Online Method install guide](https://dortania.github.io/OpenCore-Install-Guide/installer-guide/mac-install-recovery.html)).
12. You can place the resulting com.apple.recovery.boot (that appears in the same folder) and the EFI folder together at a convenient place (eg. in a folder named Tahoe).
13. Plug in your flash drive, launch Rufus, select the flash drive, set it as non bootable, and format it.
14. Open the flash drive in Explorer, clear all contents, and copy the EFI and com.apple.recovery.boot to this flash drive.
15. Open Disk Management (Win+R, and type diskmgmt.msc, and run). If you don't have an empty partition, right-click on a large partion > Shrink, and select the amout of space in MB you want for your new partition. Right click in the unallocated space > new volume. Format it is exFAT (and not NTFS), and name it Tahoe.
16. Reboot into BIOS (spam the F2 key).
17. Head to the secure boot tab, and disable it.
18. Head to the storage tab, and proceed only if SATA Operation is set to AHCI. If it's set to RAID, boot into windows, hit Win+R, type msconfig, enter, and in the Boot Tab, check Safe Boot (minimal). Reboot, and you'll see windows in safe mode. Boot into BIOS, and set the SATA operation to AHCI, save and reboot. You'll be taken to safe mode in Windows, hit Win+R, type msconfig, enter, in the Boot Tab, uncheck Safe Boot, and reboot. You'll boot normally into Windows. Now, Boot back into BIOS.
19. Head to the boot tab, and drag the Flash drive up to the highest priority. (You can also use the one time boot menu by spamming the F12 key when you reboot and select your flash drive from there, but you'll have to do so each time you reboot).
20. Save changes and reboot. This time you will boot into the open core boot loader (from your flash drive).
21. Use left/right arrow keys to head to your flash drive (dmg) which will have a yellow-tinted settings icon, and hit enter. This will take you to macOS recovery.
22. Select Disk Utility and continue (You will need to press on your trackpad to click instead of a mere tap).
23. Select Tahoe in the sidebar > erase, and format it as APFS. Exit
24. Plug in your Android device with USB, and turn on USB Tethering
25. Select Reinsatll macOS Tahoe, continue, and choose Tahoe as the install destination, and follow through the instructions. The installation wil take about 15-20 minutes with many reboots until you're greeted with macOS setup. Complete your setup.
26. Download OCAT, open, head to settings > privacy, scroll down, and open anyways. hit Cmd+M, select your internal drive, and mount. In Finder, Open NONAME in the sidebar, and open the EFI partition. Copy the Boot and OC folder from the EFI partion from your flash drive here, and select "merge".
27. Download OCLP-X, and run post install root patches
28. Download https://bitbucket.org/RehabMan/os-x-eapd-codec-commander/downloads/RehabMan-CodecCommander-2018-1003.zip and paste the kext that is inside into `/Library/Extensions` (NOT `/System/Library/Extensions`)
29. Rebuild KextCache (if you're on Catalina or newer, use Hackintool: Utilities->Kext Reload icon; if you're on an older macOS, use "sudo kextcache -i /")
30. If done properly, macOS will alert you that an extension has been updated/loaded and thus you should approve it in Settings. Then reboot, optionally resetting NVRAM.
31. If you don't see the opencore bootloader without your flash drive, head to BIOS, and add boot option in path `\EFI\OC\OpenCore.efi`. Set that to the highest priority, and reboot.


