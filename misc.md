#### triple boot on macbook air 2012 (A1466)

* [Suspicious MBR at sector 0, is there a way to fix it?](https://superuser.com/questions/1140907/suspicious-mbr-at-sector-0-is-there-a-way-to-fix-it)
   * [cite](https://superuser.com/a/1142088/673168): That's a hybrid MBR. It's necessary to boot Windows in BIOS mode, but if you plan to install Windows 8 or 10 in EFI mode, you need to replace the hybrid MBR with a legal protective MBR. You can do this with my GPT fdisk (gdisk) program:
        - Launch gdisk on the disk (sudo gdisk /dev/disk0 in your case).
        - Type x to enter experts' mode.
        - Type n to create a new protective MBR.
        - Type 'w` to save your changes.
        - Type y to confirm that you want to save the changes.
    
   * download [gdisk](https://sourceforge.net/projects/gptfdisk/files/gptfdisk/)

* [HOW-TO Triple Boot Mountain Lion, Windows 7, LMDE](https://forums.linuxmint.com/viewtopic.php?t=132603)

* [touchpad dirver patch](https://www.douban.com/note/868175161/?_i=2043220h3i97ks)、[mac-precision-touchpad](https://github.com/imbushuo/mac-precision-touchpad/releases)
