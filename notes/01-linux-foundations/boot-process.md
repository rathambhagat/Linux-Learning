# Linux Basic and System Startup

## Boot Process

When you power on the computer, the Basic Input/Output System (BIOS) begins the startup process.

BIOS (Basic Input/Output System) is a small program stored on a read-only memory (ROM) chip on a computer’s motherboard. It is the first software that runs when you power on your computer, even before the operating system is involved.

The BIOS performs a series of checks and initializations known as the Power-On Self-Test (POST). During POST, the BIOS initializes critical hardware components such as the screen, keyboard, and memory, ensuring that everything is functioning correctly before the operating system loads. The BIOS also finds and loads the bootloader, which then starts the operating system.

Once the BIOS finishes its checks, it hands control over to the next stage of the boot process, which is managed by the operating system.

## Master Boot Record, EFI Partition, and Boot Loader

Once the Power-On Self-Test (POST) is complete, control of the system passes from the BIOS to the boot loader. The boot loader is a small program stored on one of the system’s storage devices, typically a hard disk or solid-state drive.

On traditional BIOS/MBR systems, the boot loader resides in the boot sector. On more modern systems that use the (Unified) Extensible Firmware Interface (EFI or UEFI), it is stored in the EFI partition. Up to this point, the computer has not yet accessed its mass storage devices. Afterward, the system retrieves information such as the current date, time, and hardware configuration from the CMOS. This small battery-powered memory chip retains data even when the system is powered off.

- GRUB (GRand Unified Boot Loader) – the most common boot loader for Linux distributions
- ISOLINUX – used for booting from removable media such as CDs or USB drives
- DAS U-Boot – commonly used for embedded systems and appliances

Most Linux boot loaders provide a menu or user interface that allows you to choose between multiple operating systems or different Linux kernel versions. When you select a Linux option, the boot loader’s main job is to load the kernel image and the initial RAM disk (initrd or initramfs) into memory. These components contain essential drivers and files that keep the system booting until the whole operating system is loaded.

## Boot Loader

The boot loader operates in two main stages, each responsible for specific tasks in the boot process.

For systems using the BIOS/MBR method, the boot loader is stored in the first sector of the hard disk, known as the Master Boot Record (MBR). This sector is only 512 bytes in size, so it can hold only minimal code. During this stage, the boot loader:

1. Examines the partition table to locate a bootable partition.
2. Loads the second-stage boot loader (such as GRUB) into RAM (Random Access Memory).

For systems using the EFI/UEFI method, the process is more advanced. The UEFI firmware reads its Boot Manager data to determine which UEFI application should be launched and where it is located, specifically, which disk and partition contain the EFI System Partition (ESP). The firmware then launches the defined UEFI application (for example, GRUB), as specified in the boot entry. Although this procedure is more complex than the traditional MBR approach, it offers much greater flexibility and configurability.

The second-stage boot loader is typically stored under the `/boot` directory. At this stage:

1. A splash screen appears, allowing you to choose which operating system (OS) and/or kernel version to boot.
2. Once the OS and the kernel are selected, the boot loader loads the kernel into RAM and passes control to it.

Because the kernel is almost always compressed, its first task is to uncompress itself. After this, the kernel checks and analyzes the system hardware, initializing any built-in device drivers so the operating system can continue to load properly.
