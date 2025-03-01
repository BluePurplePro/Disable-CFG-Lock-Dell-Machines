# DELL-CFG-Unlock


# Preparation
- Windows operating system-duh!
- Turn off Hide extensions for known file types

![Hide extensions for known file types](https://github.com/user-attachments/assets/44d8863c-a4a4-4c96-8995-dfdfef7560e0)
- [modGRUBShell.efi](https://github.com/datasone/grub-mod-setup_var/releases)
- [VerifyMsrE2.efi](https://github.com/acidanthera/OpenCorePkg/releases)
- [UEFITool & UEFIPatch](https://github.com/LongSoft/UEFITool/releases/tag/0.28.0)
- [IFRExtract](https://github.com/LongSoft/IFRExtractor-RS/releases)
- [Intel ME System Tools](https://comsystem-tlt.ru/obzori/me-txe-region)
> [!IMPORTANT]
> It is crutial to choose the right Intel ME System Tools for your motherboard. You can check what kind of chipsest/southbridge using [CPU-Z](https://www.cpuid.com/softwares/cpu-z.html)
> ![CPU-Z](https://github.com/user-attachments/assets/4eef748d-30ff-4842-b004-fa24ec868b9b)
>
> For example, Intel ME System Tools v8 r3 (7-Series systems which come with ME firmware v8) is the right version for my motherboard since the southbridge is QM77

## Create a backup BIOS using [Intel ME System Tools](https://comsystem-tlt.ru/obzori/me-txe-region)
- Extract Intel ME System Tools (Assuming all downloaded files are in %userprofile%\Downloads)
- Go to %userprofile%\Downloads\Intel ME System Tools\Flash Programming Tool\Windows64 and create a new text document

![Create new text document](https://github.com/user-attachments/assets/71743be5-2c7f-42e6-bd5d-cc3c3b2ca86a)

- Edit the new text document, type/copy these command line and save it.
```
cd /d %~dp0
fptw64.exe -bios -d backup.fd
```
- Rename ``New Text Document.txt`` to ``backupbios.bat``. Run ``backupbios.bat`` as Administrator to create ``backup.fd`` file

![backupbios bat](https://github.com/user-attachments/assets/30c9c768-2053-4347-b33b-239a249f263d)

## Patch the BIOS ``backup.fd`` file
- Extract UEFIPatch
- Copy ``backup.fd`` file from step 1 to %userprofile%\Downloads (same folder with UEFIPatch)
- Open Command Promt and type ``cd %userprofile%\Downloads`` then press Enter
- Type ```UEFIPatch.exe backup.fd``` then press Enter to patch the backup.fd file

 ![UEFIPatch](https://github.com/user-attachments/assets/ca819316-f2e6-4c87-863c-6e549585651f)

- UEFIPatch will generate ``backup.fd.patched`` file. Rename ``backup.fd.patched`` to ``flash.fd``

## Unlock the BIOS
- Extract UEFITool
- Open backup.fd file using UEFITool.exe and find **BIOS Lock** in the **Text** tab ~> Press Enter

![UEFITool BIOS Lock](https://github.com/user-attachments/assets/32488592-4daa-418c-bf32-389072008f65)

- Select what the search returned. Right click and select **Extract as is...**

![Right click Extract as is](https://github.com/user-attachments/assets/1588d6b5-5445-4d16-be87-381b84276152)

- Name the file .sct/.bin as BIOSLock and save.

![Save as BIOS Lock](https://github.com/user-attachments/assets/46d1a964-2afd-4f24-a721-e8a478fdb9c1)

- Extract IFRExtract, then open Command Prompt and type ``ifrextractor.exe BIOSLock.sct`` or ``ifrextractor.exe BIOSLock.bin``

![Extract the BIOSLock](https://github.com/user-attachments/assets/db1afd80-d683-4083-861a-84138fc9fe2e)

- IRFExtract will generate **BIOSLock.sct.0.0.en-US.uefi.ifr.txt** file or **BIOSLock.bin.0.0.en-US.uefi.ifr.txt** file. Open the new txt file and find **BIOS Lock**. Check for **VarOffset**, **VarStoreInfo** or **Varname** value and write it down a note or something (in my case it is ``0x40``)

![BIOS Lock search](https://github.com/user-attachments/assets/dd613251-c839-4795-b0a3-3e8a3c3a08cf)

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




# Credit

https://github.com/xCuri0/ReBarUEFI/wiki/Using-UEFIPatch
