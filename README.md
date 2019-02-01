# Mac OSX on Dell-Inspiron-3543 (Hackintosh Guide)

# Laptop Specs
- CPU Intel i3-5005u
- Graphics Intel HD 5500
- RAM 8GB
- HDD 500GB
- WiFi AR9285 (replaced stock wifi card DW1704)
- Audio ALC 255 (3234)

### Pre Conditions 
Set BIOS as follows,
- Intel SpeedStep: Enabled
- Virtualization: Disabled
- Integrated NIC: Enabled
- USB Emulation: Enabled
- USB Wake Support: Disabled
- SATA Operation: AHCI
- Computrace: Deactivate

## 1. Change DVMT settings
Update BIOS to Latest, A10 as of writing

** NOTE = Below procedure will put laptop into Manufacturing Level, So Obtain your Service Tag (You can find the sticker under the laptop, 7 Chars) **
1. Download the BIOS file
2. Download the AMI Firmware Update Utility afuwin64.zip file
3. Extract afuwin64.zip file to desktop & put the BIOS File in the afuwin64 folder
4. Extract the EFI-shell.zip in the tools folder to a USB drive
5. Open a Command Prompt in Windows
6. To extract the ROM file, run the command `/writeromfile` as follows,  
`C:\Users\ABC\Desktop\afuwin64\3443A10.EXE /writeromfile`

7. Run AFUWINGUI & click open and select extracted BIOS ROM file
8. Navigate to the Setup tab and set the Block Options to Program All Blocks, it should look like this now
![afuwin64](https://user-images.githubusercontent.com/22576836/52126441-6203c280-2655-11e9-847a-f20b0a97583d.png)

9. Click Flash and wait till it finishes and reboot
10. Will prompt FN+X to enter normal mode and enter Service Tag, model
11. Boot system with the USB and select UEFI Boot, at the grub prompt, enter these commands, hit enter after each command, then exit and reboot.  
`setup_var 0x229 0x3`  
`setup_var 0x22A 0x3`  

And now you have successfully changed the DVMT settings anf fixed the kernel panic for graphics

## 2. Create OSX Installer

1. Download Mojave (I am using 10.14.1) from Olarila [here](https://olarila.com/forum/viewtopic.php?f=51&t=6743 "here")
2. Download [this](https://sourceforge.net/projects/win32diskimager/ "this")
3. Extract the .raw image to desktop 
4. Open win32diskimager and plug a 16GB+ USB & select the Mac OSX image and click write
![win32disk](https://user-images.githubusercontent.com/22576836/52126435-5ca67800-2655-11e9-8c0d-66879abaadba.png)
5. Replace EFI with my Initial EFI Install.

## 3. Install OSX

1. Boot to created USB OSX installer
2. Follow the onscreen guide and Format Hard Drive and install

## 4. Post Install

1. Assuming the USB installer is still plugged in, open Olarila Image/Files folder and copy the ESP Mounter Pro app to the Desktop .
2. Copy the EFI Post Install to the desktop
3. Open the ESP Mounter Pro app and mount the EFI folder
4. Delete the folders/files in the EFI partition and copy the EFI from the desktop 
5. Download the kexts and move all the kexts to /Library/Extensions
6. Open terminal and execute the below commands and reboot,

`sudo chmod -R 755 /Library/Extensions`  
`sudo chown -R root:wheel /Library/Extensions`  
`sudo kextcache -i /`  

Now graphics, brightness controls and WiFi are working, but Audio is not working yet, DSDT patching is needed.

## 5. DSDT & SSDT Patching
1. Download and install MaciASL [here](https://bitbucket.org/RehabMan/os-x-maciasl-patchmatic/downloads/ "here")
2. Download iASL from [here](https://bitbucket.org/RehabMan/acpica/downloads/ "here")
3. Assuming downloaded iASL is in your downloads folder, execute the following commands in terminal  
`cd ~/Downloads`  
`unzip iasl.zip`  
`sudo cp iasl /usr/bin`  

4. Reboot to Clover and press F4, this will extract the relevant files to the EFI origin folder
5. Now boot to Mac and mount EFI partition
6. Go to EFI/Clover/ACPI/Origin and copy DSDT.aml to the Desktop
7. Download the refs.txt from the tools folder to the desktop
8. Execute the following commands,  
`cd Desktop`  
`iasl -da -dl -fe refs.txt DSDT.aml`  

9. Assuming you have the decompiled DSDT.dsl file on your desktop open the DSDT in MaciASL
10. Patch the DSDT with the following,  
`USB3_PRW 0x6D (Instant Wake)`  
`IRQ Fix`  
11. Compile it and save the file as "ACPI Machine Language Binary" and will have "DSDT.aml" as the file name (Delete the previous DSDT file before saving the patched DSDT)
12. Move the patched DSDT to EFI/Clover/ACPI/Patched and reboot

The other patches that are needed such as GFX0 to IGPU & BOD3 to HDAU are done by clover hotpatch in the config.plist

*Refer Rehabman guide for more details [here](https://www.tonymacx86.com/threads/guide-patching-laptop-dsdt-ssdts.152573/ "here")*

## 6. Power Management 
Broadwell CPU's dont need to generate the SSDT using ssdtPRGen.sh script by Pike R. Alpha.
The Power Management is implemented by the SSDT-XCPM which is already in the CLOVER/ACPI/Patched folder

But we need to disable hibernation since its not supported, execute the following commands,  
`sudo pmset -a hibernatemode 0`  
`sudo rm /var/vm/sleepimage`  
`sudo mkdir /var/vm/sleepimage`  

*Refer Rehabman guide for more details [here](https://www.tonymacx86.com/threads/guide-native-power-management-for-laptops.175801/ "here")*

## FINISH
The Hackintosh should have everything working by now,
- Audio
- Graphics (Brightness Control)
- Sleep/Wake
- Ethernet
- WiFi



##  Other
### Sleep/Wake Issue
If this issue exist please go to `/Library/Preferences` and delete all the com.apple.PowerManagement.* files then reboot

macOS will re-generate the com.apple.PowerManagement* files

*Refer [here](https://www.tonymacx86.com/threads/solved-sleep-shutdown.260947/post-1814160 "here") for more details *
