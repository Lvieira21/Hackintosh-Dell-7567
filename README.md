# Personal Hackintosh Guide/Instructions for Dell 7567

### Notice
This fork of [Nihhaar](https://www.github.com/Nihhaar/) uses about the same config as he made (DSDT, SSDT patches and kexts), the only difference is that I managed to work out a way to make the wireless card DW1560 work with his hackintosh EFI.

### Screenshot
![macOS High Sierra - 10.13.6](https://raw.githubusercontent.com/Lvieira21/Hackintosh-Dell-7567/master/Assets/ScreenShot.png)

### Download Required Files
Check [releases](https://github.com/Lvieira21/Hackintosh-Dell-7567/releases)
```
Clover USB Files:
 - drivers64UEFI: HFSPlus.efi (for HFS+ fs), apfs.efi (for apfs fs)

 - kexts/Other: 
   - ApplePS2SmartTouchpad: For initial trackpad & keyboard support
   - FakeSMC: SMC emulator
   - RealtekRT8111: Kext for ethernet support
   - SATA-100-series-unsupported: 
   - USBInjectAll: Injecting USB ports (even for recognizing the bootable USB)

 - config.plist: Initial SMBIOS, USBInjectAll dsdt patches, port limit patches (for usb 3.0)

----------------------------------

Clover Post-Install Files:
 - drivers64UEFI: HFSPlus.efi (for HFS+ fs), apfs.efi (for apfs fs)

 - /L/E Kexts:
   - ACPIBatteryManager: Kext for battery status
   - AppleBacklightFixup: Kext for backlight control
   - CodecCommander: Kext for solving 'no audio' after sleep
   - VoodooPS2Controller: Kext for keyboard

 - kexts/Other:
   - AppleALC: Kext for audio (1.2.8)
   - AppleBacklightFixup: Kext for backlight control even in recovery 
   - BrcmFirmwareRepo.kext: Kext for Wireless card
   - BrcmPatchRAM2.kext: Kext for Wireless card
   - FakePCIID_Broadcom_WiFi.kext: Kext for Wireless card
   - FakePCIID.kext: Kext for Wireless card
   - FakeSMC: SMC emulator
   - Lilu: Generic kext patches (1.2.8)
   - RealtekRT8111: Kext for ethernet support
   - SATA-100-series-unsupported: 
   - USBInjectAll: Injecting USB ports
   - VoodooI2C*: Kext for precision trackpad
   - VoodooPS2Controller: Kext for keyboard
   - WhateverGreen: Lilu plugin for various iGPU patches (1.2.0)

 - config.plist:
   - DSDT Fixes: FixHPET, FixHeaders, FixIPIC, FixRTC, FixTMR
   - DSDT Patches: IGPU, IMEI, HDEF, OSI, PRW, VoodooI2C, brightness control patches
   - WhateverGreen properties: Disable unused ports, increase VRAM from 1536->2048 MB
   - Kernel and Kext Patches: DellSMBIOS, AppleRTC, KernelLapic, KernelPm
   - Kernel To Patch: MSR 0xE2, Panic kext logging
   - Kexts to Patch: I2C, SSD Trim, AppleALC patches
   - SMBIOS

 - patched:
   - SSDT-ALS0: Fake ambient light sensor
   - SSDT-BRT6: Brightness control via keyboard
   - SSDT-Disable_DGPU: Disable discrete GPU (Nvidia)
   - SSDT-I2C: VoodooI2C GPIO pinning & disabling VoodooPS2 kext for trackpads and mouses
   - SSDT-PNLF: Backlight ssdt
   - SSDT-PRW: SSDT for usb instant wake
   - SSDT-UIAC: Injecting right usb ports (coniguration for usbinjectall kext)
   - SSDT-XCPM: Injecting plugin-type for power management
   - SSDT-XOSI: Faking OS for the ACPI
   - SSDT_ALC256: CodecCommander config for ALC256 (removes headphone noise)

```

### Notes
 - Currently using in 10.13.x (High Sierra), don't know about Mojave
 - No support for 4k *(I don't have a 4k variant)*. But I think it's just `my files` + `CoreDisplayFixup.kext` + `DVMT patch`
 - HDMI doesn't work on this hack because it is connected to nvidia card and we disabled it
 - Wi-Fi, bluetooth, handoff and airdrop working flawlessly by replacing the intel wireless card to DW1560 and installing the right kexts (which are in Post-Install Kexts folder).

### Known Bugs
 - Stock wifi doesn't work (needs to be replaced with a compatible one)
 - 2.1 audio (2.0 works)
 - Audio via headphones after sleep

### Specs
 - Intel i7-7700HQ CPU
 - Intel HD Graphics 630 / nVidia GTX 1050 Ti
 - 16GB 2400MHz DDR4 RAM
 - 15.6” 1080p IPS Display
 - 256GB SanDisk M.2 SSD (SATA)
 - 500GB SanDisk SSD Sata3


## Troubleshooting (By Nihhaar)
**1) Backlight level is not persisting across reboot**
 - Boot into macOS
 - Mount EFI folder and delete nvram.plist in the folder
 - Clear nvram variables from terminal using `sudo nvram -c`
 - Reboot into Clover
 - Open Clover UEFI Shell and type following commands:
   ```bash
   FS0: 
   cd EFI 
   dmpstore -all -s dmpvars.txt
   ```
 - Find the GUID of the `backlight-level` variable (same for all macOS nvram variables) and type the following command in the same shell:
   ```bash
   dmpstore -d -guid <GUID of macOS variables> 
   exit
   ```
 - Reboot  

**2) Sound is not working**
 - Some higher versions of AppleALC doesn't seem to work properly. So install a tested version like 1.2.8
 - Make sure Lilu is installed & AppleALC is loaded after Lilu: If you use /L/E for kexts, make sure you have LiluFriend.kext and correctly configured
 - If you have FakePCIID kexts, remove them. These patches are already included in AppleALC(atleast in 1.2.8) and are not needed
 - Make sure kexts are loading properly. Sometimes kexts fail to load due to improper permissions. Use `Scripts/fixPersmissions.sh`
 - If none of the above worked, try posting in forums like tonymacx86.com  

**3) Fonts look blurry in mojave**
 - Apple nuked subpixel antialiasing in mojave, making non-retina screens blurry
 - This effect is noticable in 1080p but doesnt affect 4k or retina displays
 - The following makes things somewhat better:
```bash
# Type these commands in terminal and re-login to see the changes

defaults write -g CGFontRenderingFontSmoothingDisabled -bool NO
defaults -currentHost write -globalDomain AppleFontSmoothing -int 2
```
 - If you want to revert back to original settings:
```bash
# Type these commands in terminal and re-login to see the changes

defaults -currentHost delete -globalDomain AppleFontSmoothing
defaults write -g CGFontRenderingFontSmoothingDisabled -bool YES
```

**4) Scrolling is choppy with third party mice**
 - Use this tool: https://mos.caldis.me/, works well
 - Scroll direction can also be set via this tool

**5) Clover USB is not booting**
 - Probably needs port limit patch in config.plist for the OS version you are using (just google it)
 - Preferably, use USB 2.0 for avoiding port limit errors

## Credits
 - [Rehabman](https://www.tonymacx86.com/members/rehabman.429483/)
 - [AGuyWhoIsBored](https://www.tonymacx86.com/members/aguywhoisbored.1105835/)
 - [Nihhaar](https://www.github.com/Nihhaar/) *(Putting everything together and making various patches)*

## References
 - https://www.tonymacx86.com/threads/guide-dell-inspiron-15-7567-and-similar-near-full-functionality.234988/
 - https://www.tonymacx86.com/threads/guide-booting-the-os-x-installer-on-laptops-with-clover.148093/
 - https://www.tonymacx86.com/threads/guide-avoid-apfs-conversion-on-high-sierra-update-or-fresh-install.232855/
 - https://www.tonymacx86.com/threads/guide-laptop-backlight-control-using-applebacklightinjector-kext.218222/
 - https://www.tonymacx86.com/threads/guide-patching-dsdt-ssdt-for-laptop-backlight-control.152659/
 - https://www.tonymacx86.com/threads/guide-creating-a-custom-ssdt-for-usbinjectall-kext.211311/
 - https://www.tonymacx86.com/threads/guide-native-power-management-for-laptops.175801/
 - https://www.tonymacx86.com/threads/solved-backlight-turns-to-full-brightness-after-sleep-or-restart.192065/page-4
 - https://www.reddit.com/r/apple/comments/8wpk18/macos_mojave_nukes_subpixel_antialiasing_making/
