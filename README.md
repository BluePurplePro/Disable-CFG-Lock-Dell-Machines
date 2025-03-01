# Disable-CFG-Lock-Dell-Machines
This repository will guide you how to disable CFG Lock on Dell Machines. Useful for those who can't find ``CFG Lock`` or ``MSR Lock`` Unicode text in their extracted BIOS file that [Fixing CFG Lock from Dortania's OpenCore Post Install Guide](https://dortania.github.io/OpenCore-Post-Install/misc/msr-lock.html) and [dreamwhite's bios extraction guide](https://github.com/dreamwhite/bios-extraction-guide) covered.

# Preparation
- [modGRUBShell.efi](https://github.com/datasone/grub-mod-setup_var/releases)
- [UEFITool & UEFIPatch](https://github.com/LongSoft/UEFITool/releases/tag/0.28.0)
- [IFRExtract](https://github.com/LongSoft/IFRExtractor-RS/releases)
- [Intel ME System Tools](https://comsystem-tlt.ru/obzori/me-txe-region)
> [!IMPORTANT]
> It is crutial to choose the right Intel ME System Tools for your motherboard. You can check what kind of chipset/southbridge using [CPU-Z](https://www.cpuid.com/softwares/cpu-z.html)
> ![CPU-Z](https://github.com/user-attachments/assets/4eef748d-30ff-4842-b004-fa24ec868b9b)
>
> For example, Intel ME System Tools v8 r3 (7-Series systems which come with ME firmware v8) is the right version for my motherboard since the southbridge is QM77
- Windows operating system-duh!
- Turn off **Hide extensions for known file types** on **File Explorer Options** ~> **View**

![Hide extensions for known file types](https://github.com/user-attachments/assets/44d8863c-a4a4-4c96-8995-dfdfef7560e0)

## I. Dump your BIOS

<details>

<summary>Dump your BIOS</summary>

- Extract [Intel ME System Tools](https://comsystem-tlt.ru/obzori/me-txe-region) (Assuming all downloaded files are in `%userprofile%\Downloads`)
- Go to `%userprofile%\Downloads\Intel ME System Tools\Flash Programming Tool\Windows64` and create a new text document

![Create new text document](https://github.com/user-attachments/assets/71743be5-2c7f-42e6-bd5d-cc3c3b2ca86a)

- Edit the new text document, type/copy these command line and save it.
```
cd /d %~dp0
fptw64.exe -bios -d backup.fd
```
- Rename ``New Text Document.txt`` to ``backupbios.bat``. Run ``backupbios.bat`` as Administrator to create ``backup.fd`` file

![backupbios bat](https://github.com/user-attachments/assets/30c9c768-2053-4347-b33b-239a249f263d)

</details>

## II. Patch the BIOS file

<details>

<summary>Patch the BIOS file</summary>

- Extract [UEFIPatch](https://github.com/LongSoft/UEFITool/releases/tag/0.28.0)
- Copy ``backup.fd`` file from [section I](https://github.com/BluePurplePro/Disable-CFG-Lock-Dell-Machines?tab=readme-ov-file#i-dump-your-bios) to `%userprofile%\Downloads` (same folder with UEFIPatch)
- Open Command Promt and type ``cd %userprofile%\Downloads`` then press Enter
- Type ```UEFIPatch.exe backup.fd``` then press Enter to patch the backup.fd file

 ![UEFIPatch](https://github.com/user-attachments/assets/ca819316-f2e6-4c87-863c-6e549585651f)

- UEFIPatch will generate ``backup.fd.patched`` file. Rename ``backup.fd.patched`` to ``flash.fd``
- Copy the ``flash.fd`` file to `%userprofile%\Downloads\Intel ME System Tools\Flash Programming Tool\Windows64`

![Copy flash fd to userprofile_downloads_intel me system tools](https://github.com/user-attachments/assets/497f97ee-d0da-4326-8145-a3600ec0d86a)

</details>
 
## III. Unlock the BIOS

<details>

<summary>Unlock the BIOS</summary>

- Extract [UEFITool](https://github.com/LongSoft/UEFITool/releases)
- Open backup.fd file using UEFITool.exe and find **BIOS Lock** in the **Text** tab ~> Press Enter

![UEFITool BIOS Lock](https://github.com/user-attachments/assets/32488592-4daa-418c-bf32-389072008f65)

- Select what the search returned. Right click and select **Extract as is...**

![Right click Extract as is](https://github.com/user-attachments/assets/1588d6b5-5445-4d16-be87-381b84276152)

- Name the .sct file as BIOSLock and save.

![Save as BIOS Lock](https://github.com/user-attachments/assets/46d1a964-2afd-4f24-a721-e8a478fdb9c1)

- Extract [IFRExtract](https://github.com/LongSoft/IFRExtractor-RS/releases), then open Command Prompt and type ``ifrextractor.exe BIOSLock.sct``

![Extract the BIOSLock](https://github.com/user-attachments/assets/db1afd80-d683-4083-861a-84138fc9fe2e)

- Open **BIOSLock.sct.0.0.en-US.uefi.ifr.txt** file generated from IFRExtract and find **BIOS Lock**. Check for **VarOffset**, **VarStoreInfo** or **Varname** value `0xYY` and write it down a note or something (in my case it is ``0x40``)

![BIOS Lock search](https://github.com/user-attachments/assets/dd613251-c839-4795-b0a3-3e8a3c3a08cf)

- Boot from [modGRUBShell.efi](https://github.com/datasone/grub-mod-setup_var/releases):
  - Through a UEFI shell (navigate the FS with `cd` and `ls` basic UNIX navigation commands). Find the `EFI partition` where the `modGRUBShell.efi` file is located
  - Or through OpenCore Bootloader (by adding it to `config.plist` under `Misc/Tools` context)

![modGRUBShell efi](https://github.com/user-attachments/assets/7eb49935-2c65-4886-8d76-9ba2e1dd55a4)

- Type ``setup_var 0xYY`` (``YY`` corressponds to the **VarOffset**, **VarStoreInfo** or **Varname** you found earlier) and hit Enter. The value usually returns 0x01 (which means BIOS Lock is enabled)

![Type setup_var 0xYY](https://github.com/user-attachments/assets/12281f6f-2e37-4619-83c5-f49ad0c69099)

- Type ``setup_var 0xYY 0x00`` and press Enter to unlock the BIOS

![Type setup_var 0xYY 0x00](https://github.com/user-attachments/assets/ce2d4aa1-5b10-465a-a5c2-0e89ac11c804)

 - After that, type ``exit`` and reboot back to Windows

</details>

## IV. Disable CFG Lock

<details>

<summary>Disable CFG Lock</summary>

- Go to `%userprofile%\Downloads\Intel ME System Tools\Flash Programming Tool\Windows64` and create a new text document
- Edit the new text document, type/copy these command line and save it.
```
cd /d %~dp0
fptw64.exe -bios -f flash.fd
```

![flashbios bat](https://github.com/user-attachments/assets/90f62dc5-16e3-4d51-bda6-143489c32d69)

- Rename ``New Text Document.txt`` to ``flashbiot.bat``. Run ``flashbios.bat`` as Administrator

![And now we wait](https://github.com/user-attachments/assets/d9fe5efd-3d41-4b75-9135-fee1097af814)

- The CFG Lock should be disabled after the Command Prompt finished patching. Checking via [ControlMsrE2.efi](https://github.com/acidanthera/OpenCorePkg/releases) should provide:

```This firmware has UNLOCKED MSR 0xE2 register!```

![ControlMsrE2](https://github.com/user-attachments/assets/5c15d1c7-6cce-4367-8ba2-5f4be817d139)

![This firmware has UNLOCKED MSR 0xE2](https://github.com/user-attachments/assets/16a5d66e-a2b7-41c5-a7a2-6af38e2c63ed)

- Which means CFG Lock is disabled.

</details>

# Credit
- [acidanthera](https://github.com/acidanthera) for [OpenCore Bootloader](https://github.com/acidanthera/OpenCorePkg)
- [Dotarnia](https://github.com/dortania) for [Fixing CFG Lock](https://dortania.github.io/OpenCore-Post-Install/misc/msr-lock.html#fixing-cfg-lock)
- [dreamwhite](https://github.com/dreamwhite) for [bios extraction guide](https://github.com/dreamwhite/bios-extraction-guide)
- [platomav](https://github.com/platomav) for [Intel ME System Tools](https://comsystem-tlt.ru/obzori/me-txe-region)
- [LongSoft](https://github.com/LongSoft) for [UEFITool](https://github.com/LongSoft/UEFITool), [UEFIPatch](https://github.com/LongSoft/UEFITool/releases/tag/0.28.0) and [IRFExtractor](https://github.com/LongSoft/IFRExtractor-RS)
- [datasone](https://github.com/datasone) for [modGRUBShell.efi](https://github.com/datasone/grub-mod-setup_var)
- [xdnuos](https://github.com/xdnuos) for [DELL-CFG-Unlock](https://github.com/xdnuos/DELL-CFG-Unlock)
- [xCuri0](https://github.com/xCuri0) for [Using UEFIPatch](https://github.com/xCuri0/ReBarUEFI/wiki/Using-UEFIPatch)
 
