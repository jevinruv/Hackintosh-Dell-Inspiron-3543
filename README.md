# Mac OSX on Dell-Inspiron-3543 (Hackintosh Guide)

# Laptop Specs
- CPU Intel i3-5005u
- Graphics Intel HD 5500
- RAM 8GB
- HDD 500GB
- WiFi AR9285 (replaced stock wifi card DW1704)
- Audio ALC 255 (3234)

###Pre Conditions 
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

**NOTE = Below procedure will put laptop into Manufacturing Level, So Obtain your Service Tag (You can find the sticker under the laptop, 7 Chars) **

1. Download the BIOS file
2. Download the AMI Firmware Update Utility afuwin64.zip file
3. Extract afuwin64.zip file to desktop & put the BIOS File in the afuwin64 folder
4. Extract the EFI-shell.zip in the tools folder to a USB drive
5. Open a Command Prompt in Windows
6. To extract the ROM file, run the command `/writeromfile` as follows,
`C:\Users\ABC\Desktop\afuwin64\3443A10.EXE /writeromfile`

7. Run AFUWINGUI & click open and select extracted BIOS ROM file
8. Navigate to the Setup tab and set the Block Options to Program All Blocks, it should look like this now
![](https://www.tonymacx86.com/attachments/flashing-the-bios-png.193866/)

9. Click Flash and wait till it finishes and reboot
10. Will prompt FN+X to enter normal mode and enter Service Tag, model
11. Boot system with the USB and select UEFI Boot, at the grub prompt, enter these commands, hit enter after each command, then exit and reboot.
`setup_var 0x229 0x3` and `setup_var 0x22A 0x3`

And now you have successfully changed the DVMT settings anf fixed the kernel panic for graphics

## 2. Create OSX Installer

1. Download Mojave (I am using 10.14.1) from Olarila [here](https://olarila.com/forum/viewtopic.php?f=51&t=6743 "here")
2. Download [this](https://sourceforge.net/projects/win32diskimager/ "this")
3. Extract the .raw image to desktop 
4. Open win32diskimager and plug a 16GB+ USB & select the Mac OSX image and click write
![](https://olarila.com/forum/download/file.php?id=17254)
5. Replace EFI with mine, and add the kexts to /EFI/Clover/kexts/Other

## 3. Install OSX

1. Boot to created USB OSX installer
2. Follow the onscreen guide and Format Hard Drive and install

## 4. Post Install

1. Assuming the USB installer is still plugged in, open Olarila Image/Files folder and copy the ESP Mounter Pro app to the Desktop .
2. Copy the EFI folder of the USB OSX installer to the desktop and eject it
3. Open the ESP Mounter Pro app and mount the EFI folder
4. Delete the folders/files in the EFI partition and copy the EFI from the desktop that you copied from the USB OSX installer
5. Go to /EFI/Clover/kexts/Other and move all the kexts to /Library/Extensions
6. Open terminal and execute the below commands and reboot,

`sudo chmod -R 755 /Library/Extensions`
`sudo chown -R root:wheel /Library/Extensions`
`sudo kextcache -i /`

Now graphics, brightness controls and WiFi are working, but Audio is not working yet, DSDT patching is needed.

## 5. DSDT & SSDT Patching


##  Other
### Sleep/Wake Issue
If this issue exist please go to `/Library/Preferences` and delete all the com.apple.PowerManagement.* files then reboot

macOS will re-generate the com.apple.PowerManagement* files

*thanks to [this](https://www.tonymacx86.com/threads/solved-sleep-shutdown.260947/post-1814160 "this")*


