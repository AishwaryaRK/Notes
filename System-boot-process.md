# System boot process

YOU
        Press power switch (or start a virtual machine)
        Now computer has the control
        This control is passed to BIOS
    BIOS
        Performs hardware-specific tasks
        Makes POST, Power-On Self-Test, which tests your hardware
        Detects installed hardware, such as hard disks, memory type and amount, ...
        Initializes hardware by writing initial values into their memory
        Finds a boot device, which is usually a hard disk
        Read and executes MBR (Main Boot Record) located at the beginning of this disk
        Control is not passed to MBR
    MBR
        MBR finds and executes GRUB (Grand Unified Bootloader)
        Control is now passed to GRUB
    GRUB
        Finds available filesystems
        Finds and reads its configuration file, to find out:
            Where system is located
            What system to boot
            What other actions perform
        Executes Linux Kernel, main part of Linux OS
        Control is now passed to Linux Kernel
    Linux Kernel
        Finds and loads initrd, which is initial ram disk
            initrd contains necessary drivers which allow to access and mount real filesystems
        Mounts the root file system, which is specified in GRUB config file
        Executes /sbin/init, a special program which starts all the others
        Control is now passed to init
    init
        Looks at the /etc/inittab to determine desired run level
        Loads all programs appropriate for this runlevel
            All programs from /etc/rc.d/rc2.d/ are loaded, because 2 is default Debian runlevel
            SSH and TTY are started so you are able to connect to your computer
        Booting up is now finished
    YOU
        Connect to your computer using SSH
        SSH daemon executes bash shell for you
        You can now type stuff
        You are in control once again