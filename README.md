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

**NOTE = Below procedure will put laptop into Manufacturing Level, So Obtain your Service Tag (You can find the sticker under the laptop, 7 Chars) **

1. Download the BIOS file
2. Download the AMI Firmware Update Utility (AFUWIN) zip file
3. Extract AFUWIN zip file & put the BIOS File in the AFUWIN folder
4. Extract the EFI-shell.zip in the tools folder to a USB drive
5. Open a Command Prompt in Windows
6. To extract the ROM file, run the command `/writeromfile` as follows,
`C:\Users\ABC\Desktop\afuwin64\3443A10.EXE /writeromfile`

7. Run AFUWINGUI & click open and select extracted BIOS ROM file
8. Navigate to the Setup tab and set the Block Options to Program All Blocks, it should look like this now
![](https://www.tonymacx86.com/attachments/flashing-the-bios-png.193866/)

9. Finally Click Flash and wait till it finishes and reboot
10. Will prompt FN+X to enter normal mode and enter Service Tag, model
11. Boot system with the USB and select UEFI Boot, at the grub prompt, enter these commands, hit enter after each command, then exit and reboot.
`setup_var 0x229 0x3` and `setup_var 0x22A 0x3`


## 2. Create OSX Installer


