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

## Initial Ram disk
Once the kernel decompresses and finishes its initial hardware probe, it faces a technical limitation: it needs to mount the root filesystem to access system tools, but the drivers required to talk to the storage hardware are often stored on that very filesystem. To solve this, the system uses the Initial RAM Disk (initramfs) as a temporary bridge.

The initramfs is a small, compressed filesystem image that the boot loader pulls into memory alongside the kernel. It acts as a specialized toolkit containing the binary files and programs necessary to prepare the system for its final environment.


Driver Loading via udev
The initramfs uses the udev (user device) system to scan the hardware. It identifies which mass storage controllers are present, locates the matching drivers, and loads them so the kernel can finally "see" the disk.


Filesystem Support
It provides the kernel with the specific logic needed to read the filesystem type (such as Ext4 or XFS) used by the root partition.


Validation and Mounting
Once the hardware is reachable, the initramfs locates the root filesystem, performs an error check, and prepares it for the final mounting process.

## Text Mode login 
As the boot process nears completion, the init process launches several text-mode login prompts. These prompts allow you to enter your username and password to access a command shell. If your system is configured with a graphical login interface (such as GNOME or KDE), these text prompts will be active in the background and won't be visible immediately.

Linux is designed to handle multiple sessions simultaneously through virtual terminals. Here is how you can move between them:

•
Switching in Text-Mode
You can switch between different text terminals by pressing the ALT key plus a Function key (e.g., ALT+F2).

•
Switching from a Graphical Environment
If you are currently in a GUI, you must press CTRL+ALT plus the appropriate Function key to reach a text console.

•
Standard Configuration
Most distributions provide six text terminals and one graphical terminal, usually accessible starting with the F1 or F2 keys.

•
Returning to the GUI
Depending on your distribution, pressing F1 or F7 (combined with CTRL+ALT) will typically lead you back to the graphical user interface.

## The Linux Kerne
Once the boot loader pulls the kernel and the initial RAM-based filesystem (initramfs) into memory, the kernel officially takes control of the system. As the heart of the operating system, its first priority is to transform raw hardware components into a coordinated, usable system.

As soon as the kernel is active in RAM, it begins a comprehensive configuration process to "claim" the hardware. This initialization is driven by internal kernel functions like start_kernel() and a sequence of initcalls, which systematically bring the hardware to life:

Memory Management
The kernel maps out the system’s physical RAM. It decides how to share this memory between the operating system and the applications you will run.

Processor Setup
It identifies and initializes all available CPUs. This prepares the system to handle multiple tasks simultaneously.

I/O and Storage
The kernel finds and configures all attached input/output (I/O) subsystems and storage devices. This ensures it can communicate with everything from your keyboard to your hard drive.

The kernel does more than manage hardware; it also starts the user space, which is the environment where your programs run. It uses the drivers and files stored in the initramfs to create what is known as "early userspace."


