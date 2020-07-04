# DELL-CFG-Unlock
DELL CFG UNLOCK

TRY THIS GUIDE FIRST: https://github.com/dreamwhite/bios-extraction-guide/blob/master/Dell/README.md

## Introduction

In order to extract BIOS payload from Dell BIOS upgrade package you need:

- BIOS Update package
- Intel (CS)ME System Tools (https://comsystem-tlt.ru/obzori/me-txe-region OR https://www.win-raid.com/t596f39-Intel-Management-Engine-Drivers-Firmware-amp-System-Tools.html)
(NOTE download version for your mainboard. Example I have E6530 with 7 series chipset + IntelME v8 . I download Intel ME System Tools v8 r3.rar)
- UEFITool(https://github.com/LongSoft/UEFITool/releases)
- UEFI Patch(https://github.com/LongSoft/UEFITool/releases)
- modGRUBShell.efi(https://github.com/datasone/grub-mod-setup_var/releases)
- IFRExtract(https://github.com/LongSoft/Universal-IFR-Extractorreleases)
- VerifyMsrE2.efi from OpenCorePkg EFI/OC/Tools folder](https://github.com/acidanthera/OpenCorePkg/releases)

## Step 1: Create backup bios with Intel (CS)ME System Tools
- Boot into Windows ( I use windows XP PE)
- create Backupbios.bat in Intel (CS)ME System Tools》Flash Programming Tool》WIN(64) and RUN it
Code 
cd /d %~dp0
fptw64.exe -bios -d backup.fd
- Run Backupbios.bat
- Now you have backup.fd file
## Step 2: mod bios( if you download UEFITool and UEFI patch for MAC. YOU must boot into MACOS)
- this step for macos. windows do the same
- Put UEFI Patch in Desktop
- open Teminal ( CMD)
TYPE
   cd ./desktop/UEFIpatch
   UEFIpatch backup.fd
- Now you have backup.fd.patched. Rename to flash.fd
## Step 3: UNLOCK BIOS
- PLEASE read this guide first: https://github.com/dreamwhite/bios-extraction-guide/blob/master/Dell/README.md
- Orginal guide https://www.win-raid.com/t3908f16-GUIDE-Grub-Fix-Intel-FPT-Error-or-BIOS-Lock-Asus-Other-Mod-BIOS-Flash.html
- Put backup.fd into UEFITool and FIND "BIOS Lock"
- Now you will see "BIOS Lock" in "setup" line
- Right Click "Setup" -> Extract body and rename it to BIOSlock.fdb
- Open teminal type
 <PATH IFRExtract> BIOSlock.fdb setup.txt
- Find "Bios lock" int setup.txt
- you see:" BIOS Lock, VarStoreInfo(varoffset/varname) 0xYY ( Example 0x04)
- Put modGRUBShell.efi in OC/tool and add it on config.plist (or boot using another method)
- TYPE
  setup_var 0xYY (You will see "offset is 0x01")
  setup_var 0xYY 0x00 (You will see "offset is 0x00". Now BIOS was unlock)
  Exit
## Step 4: UNLOCK CFG
- Boot into Windows ( I use windows XP PE)
- create flashbios.bat (use notepad) in Intel (CS)ME System Tools》Flash Programming Tool》WIN(64) and RUN it
Code 
cd /d %~dp0
fptw64.exe -bios -f flash.fd
- Run flashbios.bat
- WAITTTTTTTTTTT
- BOMMMMMMM BIOS has UNLOCK CFG
- USE VerifyMsrE2.efi to test
SORRY MY ENGLISH NOT GOOD




