# Linux Basic and System Startup

## Boot Process

-> When you power on the computer, the Basic Input/Output System (BIOS) begins the startup process.

-> BIOS (Basic Input/Output System) is a small program stored on a read-only memory (ROM) chip on a computer’s motherboard. 

-> It is the first software that runs when you power on your computer, even before the operating system is involved.

-> The BIOS performs a series of checks and initializations known as the Power-On Self-Test (POST). During POST, the BIOS initializes critical hardware components such as the screen, keyboard, and memory, ensuring that everything is functioning correctly before the operating system loads.

-> The BIOS also finds and loads the bootloader, which then starts the operating system.

-> Once the BIOS finishes its checks, it hands control over to the next stage of the boot process, which is managed by the operating system.

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

## The Linux Kernel
Once the boot loader pulls the kernel and the initial RAM-based filesystem (initramfs) into memory, the kernel officially takes control of the system. As the heart of the operating system, its first priority is to transform raw hardware components into a coordinated, usable system.

As soon as the kernel is active in RAM, it begins a comprehensive configuration process to "claim" the hardware. This initialization is driven by internal kernel functions like start_kernel() and a sequence of initcalls, which systematically bring the hardware to life:

Memory Management
The kernel maps out the system’s physical RAM. It decides how to share this memory between the operating system and the applications you will run.

Processor Setup
It identifies and initializes all available CPUs. This prepares the system to handle multiple tasks simultaneously.

I/O and Storage
The kernel finds and configures all attached input/output (I/O) subsystems and storage devices. This ensures it can communicate with everything from your keyboard to your hard drive.

The kernel does more than manage hardware; it also starts the user space, which is the environment where your programs run. It uses the drivers and files stored in the initramfs to create what is known as "early userspace."

-> The kernel does more than manage hardware; it also starts the user space, which is the environment where your programs run. It uses the drivers and files stored in the initramfs to create what is known as "early userspace."

-> This early userspace is critical because it contains the logic required to find, check, and mount the primary root filesystem on your disk. Once that filesystem is verified and ready, the kernel transitions from this temporary environment to the permanent operating system. Finally, it loads the essential applications needed to finish the boot sequence.

## /sbin/init and Services

Once the kernel completes the hardware setup and mounts the root filesystem, it launches a critical program: /sbin/init.

/sbin/init is the first process to run on the system and serves as the "parent" of all other processes.

### The Role of init Process

Init process is Responsible for bringing the system to usable state and managing it until the computer is powered off.
Its primary responsibilities includes:

- Process Initiation
Its starts the Background services and applications required for the system to function.

- System Management
Most processes on your system can trace their origin back to init. The only exceptions are kernel processes, which the kernel starts directly to manage internal operating system details.

- Ongoing Maintenance
init acts as a manager for all non-kernel processes, cleaning up after them when they finish and restarting login services as users log in and out.

- System shutdown
It ensures that all services are stopped correctly and data is saved before the system powers down.

## Systemd features
- Parallelization
Unlike older methods that started services one by one (serialized), systemd uses aggressive parallelization. This allows multiple services to launch simultaneously, significantly reducing total boot time.

- Declarative Configuration
systemd replaces complex, error-prone shell scripts with simple .service configuration files. These files explicitly define dependencies (what must start first), execution commands, and success criteria.

- The New Init:
On a systemd-enabled machine, the traditional /sbin/init is actually a symbolic link to /lib/systemd/systemd. This means systemd is the very first process to start (PID 1) and manages all others.

The systemctl command is your primary tool for interacting with the systemd manager. Use the following syntax to manage services like the Apache web server (apache2 is common on Debian/Ubuntu; on Red Hat/Fedora it's usually httpd).

- Real Time Service Conrol
To change the state of a service on your currently running system, use start, stop, or restart, run the following command:
```
sudo systemct1 start apache2

sudo systemct1 stop apache2

sudo systemct1 restart apache2

```
- Boot-Time Configuration
To determine whether a service should launch automatically when the computer turns on, use enable or disable:
```
sudo systemct1 enable apache2

sudo systemct1 disable apache2

```
- Monitoring Service health
To check if a service is running, view its recent logs, or see its process ID (PID), use the status command:
```
sudo systemct1 montior apache2
```

## Common Linux Filesystem Types
Linux is highly versatile and supports a wide array of filesystem formats depending on the hardware and use case:

- Conventional Disk Filesystems
These are the "workhorses" for standard hard drives and SSDs. Common examples include ext4 (the most widely used), XFS (excellent for large files), and Btrfs.

- Flash Storage Filesystems
Optimized for raw flash memory (like those in embedded devices), such as ubifs or yaffs.

- Special purpose filesystem
These often exist only in memory to provide system information, such as procfs (found at /proc) or tmpfs (temporary volatile storage).

- Databse Filesystem
These are a specialized type of filesystem that treats data not as a collection of simple files and folders, but as objects or rows within a database. While standard filesystems like ext4 or NTFS are optimized for storing blocks of data on a disk, a database filesystem is designed for high-speed searching, metadata richness, and data integrity.

## Partitions and Filesystem
Imagine your hard drive as a physical office building; a partition represents a dedicated subsection of that physical media, much like a specific floor or suite. Historically, this meant a physically contiguous portion of a hard disk. While modern storage can be more complex, we still treat a partition as a fixed area to be managed as a single unit. In Linux, these partitions are identified as device files, such as /dev/sda1 for the first partition on your primary drive.

While the partition defines the physical boundaries of your storage, the filesystem is the logical structure that stores and accesses files within that space. If the partition is the empty office suite, the filesystem acts as the filing cabinets and indexing system that allows the operating system to find and retrieve data. Without a filesystem, a partition is just a "raw" collection of ones and zeros. By "formatting" a partition, you are installing a specific filesystem, such as ext4 or XFS, to organize that space.

The final step in this process is mounting, which bridges the gap between the partition and your usable folder structure. In Linux, we do not use drive letters like C: or D:. Instead, you "attach" the filesystem of a partition to a specific directory, known as a mount point. For example, you might mount the partition /dev/sdb1 to the directory /home. From that moment on, any file you save in your home folder is physically written to that specific partition. This modular approach allows Linux to treat multiple physical disks and partitions as one seamless, unified tree of files.

## The filesystem hiearchy Standard


| Directory | Purpose |
| :--- | :--- |
| **/** | The root of the entire filesystem tree. Every file and directory on the system lives beneath this point. This is roughly equivalent to C:\ on Windows, though unlike Windows, Linux does not use drive letters. |
| **/home** | Personal directories for each user (e.g., /home/student). This is equivalent to C:\Users\ on Windows and /Users/ on macOS. |
| **/root** | The home directory for the system's superuser. It is kept separate from /home for security reasons. Windows and macOS have no direct equivalent. |
| **/etc** | System-wide configuration files. If an application needs a settings file that affects all users, it lives here. macOS uses the same name, but it is a symbolic link to /private/etc. Windows stores equivalent settings in the registry and C:\Windows\System32\. |
| **/bin and /usr/bin** | Essential user-facing programs and commands (e.g., ls, cp, bash). On most modern distributions, /bin is a symbolic link to /usr/bin. Roughly equivalent to C:\Windows\System32\ on Windows. macOS uses the same directory names. |
| **/sbin and /usr/sbin** | System administration programs intended for use by the root user. macOS uses the same directory names. Windows stores equivalent utilities in C:\Windows\System32\. |
| **/lib and /usr/lib** | Shared libraries required by programs in /bin and /sbin. Equivalent to .dll files in C:\Windows\System32\ on Windows. macOS uses /usr/lib for the same purpose. |
| **/var** | Variable data that changes during normal operation, such as log files (/var/log), print queues, and mail spools. macOS uses the same name, implemented as a symbolic link to /private/var. Windows spreads equivalent data across C:\Windows\ and C:\ProgramData\. |
| **/tmp** | Temporary files created by applications. These are not guaranteed to survive a reboot. Equivalent to C:\Windows\Temp\ on Windows. macOS uses the same name, implemented as a symbolic link to /private/tmp. |
| **/dev** | Device files. Linux represents hardware (hard drives, terminals, USB devices) as files in this directory. macOS follows the same convention. Windows handles device access through the Device Manager and kernel drivers, with no user-visible equivalent. |
| **/proc** | A virtual filesystem that exposes real-time information about running processes and the kernel. Nothing here is stored on disk; it is generated on demand. Neither Windows nor macOS have a direct equivalent. |
| **/sys** | A virtual filesystem providing a structured interface to kernel and hardware information. Neither Windows nor macOS have a direct equivalent. |
| **/boot** | Files required to start the system, including the Linux kernel itself. Windows manages boot files through the Windows Boot Manager; macOS handles booting through its own firmware. Neither exposes these files in a user-navigable folder in the same way. |
| **/run** | Runtime data for processes started since the last boot, such as process ID files and lock files. Neither Windows nor macOS have a direct equivalent. |

