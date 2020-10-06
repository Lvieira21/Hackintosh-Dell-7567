# Personal Hackintosh Documentation/Instructions for Dell Gaming 7567

## Notice
For the High Sierra (10.13) Release: This fork of [Nihhaar's repo](https://www.github.com/Nihhaar/) uses about the same config as he made (DSDT, SSDT patches and kexts), the only difference is that I managed to work out a way to make the wireless card DW1560 work.

For the Catalina (10.15) Release: Initial USB kexts and config used are from [maxis7567's repo](https://github.com/maxis7567/hackintosh_dell_7567_catalina_install_guide), [Nihhaar's](https://www.github.com/Nihhaar/) kexts and config files were fused into this build and managed to continue using DW1560 (updated kexts), lastly I set up CPUFriend to stop the CPU from running at high frequency all the time. 
### Warning: CPU Friend is configured to be used with the same CPU as mine, if you have other CPU, you NEED to configure your own SSDT for CPUFriend. [Refer to this guide](https://www.olarila.com/topic/5693-guide-ssdt-with-pikes-pm-script-and-use-with-cpufriend/)

## Screenshot
![macOS Catalina - 10.15.6](https://github.com/Lvieira21/Hackintosh-Dell-7567/blob/master/Assets/Screenshot.png?raw=true)

### Download Required Files
Check [releases](https://github.com/Lvieira21/Hackintosh-Dell-7567/releases)
```
Clover USB Files:
 - ACPI: SSDT Patch needed for Catalina

 - drivers64UEFI: HFSPlus.efi (for HFS+ fs), apfs.efi (for apfs fs) and OsxAptioFix3Drv.efi

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

 - kexts/Other:
   - AirportBrcmFixup
   - CPUFriend
   - FakeSMC
   - Lilu
   - RealtekRTL8111
   - SATA-100-series-unsupported
   - USBInjectAll
   - VoodooI2C
   - VoodooI2CHID
   - VoodooPS2Controller
   - WhateverGreen

 - /L/E Kexts:
   - ACPIBatteryManager
   - AirportBrcmFixup
   - AppleALC
   - AppleBacklightInjector
   - BrcmBluetoothInjector
   - BrcmFirmwareRepo
   - BrcmPatchRAM3
   - CodecCommander
   - FakeSMC_ACPISensors
   - FakeSMC_CPUSensors
   - FakeSMC_GPUSensors
   - FakeSMC_LPCSensors
   - FakeSMC_SMMSensors
   - FakeSMC
   - Lilu
   - RealtekRTL8111
   - SATA-100-series-unsupported
   - USBInjectAll
   - VoodooI2C
   - VoodooI2CHID
   - VoodooPS2Controller
   - WhateverGreen

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
   - SSDT.aml: CPUFriend configuration

```

### Notes
 - Currently using in 10.15.6 (Catalina), don't know about Big Sur;
 - No support for 4k *(I don't have a 4k variant)*;
 - HDMI doesn't work on this hack because it is connected to nvidia card and we disabled it;
 - Wi-Fi, bluetooth, handoff and airdrop working flawlessly by replacing the intel wireless card to DW1560 and installing the right kexts (which are in Post-Install Kexts folder).

### Known Bugs
 - Stock wifi doesn't work (needs to be replaced with a compatible one);
 - 2.1 audio (2.0 works) *(I recommend the use of Boom2 App for a little bit more volume)*;
 - Audio via headphones after sleep *(Still a problem)*.

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

### Additional Guides



## Credits
 - [Rehabman](https://www.tonymacx86.com/members/rehabman.429483/)
 - [AGuyWhoIsBored](https://www.tonymacx86.com/members/aguywhoisbored.1105835/)
 - [Nihhaar](https://www.github.com/Nihhaar/) *(Putting everything together and making various patches)*
 - [maxis7567's repo] https://github.com/maxis7567/hackintosh_dell_7567_catalina_install_guide *(USB kexts and initial 10.15.x Setup)*

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
 - [SSDT with Pike's PM Script and use with CPUFriend](https://www.olarila.com/topic/5693-guide-ssdt-with-pikes-pm-script-and-use-with-cpufriend/)
 - [Creating a bootable USB for macOS 10.15.x](https://hackintosher.com/forums/thread/guide-how-to-create-a-macos-catalina-10-15-0-usb-installer-drive-w-clover.2836/)
