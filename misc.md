
* [Suspicious MBR at sector 0, is there a way to fix it?](https://superuser.com/questions/1140907/suspicious-mbr-at-sector-0-is-there-a-way-to-fix-it)
   * [cite](https://superuser.com/a/1142088/673168): That's a hybrid MBR. It's necessary to boot Windows in BIOS mode, but if you plan to install Windows 8 or 10 in EFI mode, you need to replace the hybrid MBR with a legal protective MBR. You can do this with my GPT fdisk (gdisk) program:
        - Launch gdisk on the disk (sudo gdisk /dev/disk0 in your case).
        - Type x to enter experts' mode.
        - Type n to create a new protective MBR.
        - Type 'w` to save your changes.
        - Type y to confirm that you want to save the changes.
    
   * download [gdisk](https://sourceforge.net/projects/gptfdisk/files/gptfdisk/)
