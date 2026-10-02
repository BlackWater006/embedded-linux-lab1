# Embedded Linux Lab 1

![Platform](https://img.shields.io/badge/Platform-ARMv7-blue)
![Kernel](https://img.shields.io/badge/Linux%20Kernel-5.15-orange)
![U--Boot](https://img.shields.io/badge/U--Boot-2022.04-green)
![BusyBox](https://img.shields.io/badge/BusyBox-1.35.0-purple)
![QEMU](https://img.shields.io/badge/QEMU-vexpress--a9-red)

A hands-on Embedded Linux laboratory project covering the process of building, configuring, and booting a minimal Linux system for an ARMv7 platform.

The project uses Linux Kernel 5.15, U-Boot 2022.04, BusyBox 1.35.0, an initramfs-based root filesystem, and QEMU ARM Versatile Express emulation.

---

## 1. Project Overview

The main objective of this laboratory is to understand the architecture and boot process of an embedded Linux system.

The project covers:

* Linux Kernel cross-compilation for ARMv7
* Linux Kernel configuration and build
* U-Boot configuration and build
* BusyBox configuration and build
* Minimal initramfs root filesystem
* Device Tree Blob generation
* QEMU ARM system emulation
* Linux kernel boot verification
* Basic embedded Linux userspace verification
* Git-based milestone development

The final system successfully boots Linux Kernel 5.15 on a virtual ARM Cortex-A9 processor and provides an interactive BusyBox shell.

---

## 2. System Architecture

The Embedded Linux system is organized into several layers, from the virtual hardware platform up to the userspace applications.

```text
┌───────────────────────────────────────────────────────────────┐
│                        APPLICATION                            │
│                                                               │
│                 BusyBox Utilities / Shell                     │
│                                                               │
│        ls    cat    ps    mount    echo    sh    ...         │
├───────────────────────────────────────────────────────────────┤
│                        USER SPACE                             │
│                                                               │
│     /init    /bin    /sbin    /etc    /dev    /usr    /lib  │
│                                                               │
│                     BusyBox Userspace                         │
├───────────────────────────────────────────────────────────────┤
│                       LINUX KERNEL                            │
│                           5.15                                │
│                                                               │
│   Process Management │ Memory │ VFS │ Drivers │ Networking   │
│                                                               │
│                    System Call Interface                      │
├───────────────────────────────────────────────────────────────┤
│                     VIRTUAL HARDWARE                          │
│                                                               │
│                 QEMU vexpress-a9                              │
│                 ARM Cortex-A9 / ARMv7                         │
└───────────────────────────────────────────────────────────────┘
                              ▲
                              │
                    Device Tree Blob
                    vexpress-v2p-ca9.dtb
```

### Architecture Components

| Layer                | Component        | Function                                                    |
| -------------------- | ---------------- | ----------------------------------------------------------- |
| Hardware             | QEMU vexpress-a9 | Emulates the ARM Versatile Express platform                 |
| Processor            | ARM Cortex-A9    | Target ARMv7 CPU                                            |
| Kernel               | Linux 5.15       | Provides process, memory, filesystem, and device management |
| User Space           | BusyBox          | Provides essential Linux utilities and shell                |
| Root Filesystem      | initramfs        | Provides the initial embedded Linux filesystem              |
| Hardware Description | Device Tree Blob | Describes the platform hardware to the Linux kernel         |

---

## 3. Boot Architecture

The system boot process can be represented as:

```text
┌──────────────────────┐
│    Power On / QEMU   │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│      U-Boot          │
│      2022.04         │
└──────────┬───────────┘
           │
           │ Load Kernel + DTB
           ▼
┌──────────────────────┐
│   Linux Kernel 5.15  │
│                      │
│ CPU / Memory /       │
│ Device Initialization│
└──────────┬───────────┘
           │
           │ Mount initramfs
           ▼
┌──────────────────────┐
│       /init          │
│ First userspace      │
│ process              │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│       BusyBox        │
│                      │
│ init + utilities     │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│     /bin/sh          │
│                      │
│     / #              │
└──────────────────────┘
```

The Linux kernel receives the Device Tree Blob to obtain information about the target platform and uses the initramfs as the initial root filesystem.

---

## 4. Target Platform

| Component       | Configuration          |
| --------------- | ---------------------- |
| Architecture    | ARMv7                  |
| CPU             | ARM Cortex-A9          |
| Machine         | QEMU vexpress-a9       |
| Linux Kernel    | 5.15                   |
| U-Boot          | 2022.04                |
| BusyBox         | 1.35.0                 |
| Root Filesystem | initramfs              |
| Console         | `ttyAMA0`              |
| RAM             | 512 MB                 |
| SMP             | 2 CPUs                 |
| Cross Compiler  | `arm-linux-gnueabihf-` |

---

## 5. Repository Structure

```text
embedded-linux-lab1/
│
├── bao_cao/
│   └── MSSV_Lab01_BaoCao.pdf
│
├── configs/
│   ├── kernel.config
│   └── busybox.config
│
├── output/
│   ├── initramfs.cpio.gz
│   ├── u-boot
│   ├── vexpress-v2p-ca9.dtb
│   └── zImage
│
├── rootfs/
│   └── initramfs/
│       ├── bin/
│       ├── dev/
│       ├── etc/
│       ├── init
│       ├── lib/
│       ├── proc/
│       ├── root/
│       ├── sbin/
│       ├── sys/
│       ├── tmp/
│       └── usr/
│
├── .gitignore
└── README.md
```

The complete Linux Kernel, U-Boot, and BusyBox source trees are excluded from Git tracking through `.gitignore`.

This keeps the repository focused on the configurations, final build artifacts, root filesystem, documentation, and reproducible project structure.

---

## 6. Main Components

### 6.1 Linux Kernel

The project uses Linux Kernel 5.15 configured for the ARMv7 Versatile Express platform.

The final kernel image is:

```text
output/zImage
```

The Device Tree Blob is:

```text
output/vexpress-v2p-ca9.dtb
```

The kernel is cross-compiled using:

```text
arm-linux-gnueabihf-
```

---

### 6.2 U-Boot

U-Boot 2022.04 is used as the bootloader component of the project.

The generated executable is:

```text
output/u-boot
```

U-Boot is responsible for preparing the boot environment and loading the kernel and hardware description during the embedded Linux boot process.

---

### 6.3 BusyBox

BusyBox provides the minimal userspace environment.

It combines many common Unix utilities into a single executable, including:

```text
sh
ls
cat
mount
ps
echo
mkdir
cp
mv
dmesg
```

The BusyBox configuration is stored in:

```text
configs/busybox.config
```

---

### 6.4 Initramfs

The initial root filesystem is located at:

```text
rootfs/initramfs/
```

The filesystem contains the standard directories required for the embedded Linux userspace:

```text
bin/
dev/
etc/
lib/
proc/
root/
sbin/
sys/
tmp/
usr/
```

The most important file is:

```text
rootfs/initramfs/init
```

The Linux kernel executes `/init` as the first userspace process after initializing the kernel.

The final compressed initramfs image is:

```text
output/initramfs.cpio.gz
```

---

### 6.5 Device Tree

The Device Tree Blob:

```text
output/vexpress-v2p-ca9.dtb
```

provides hardware description information to the Linux kernel.

It allows the kernel to identify and configure the virtual hardware provided by the QEMU `vexpress-a9` machine.

---

## 7. Project Workflow

The overall development workflow is:

```text
Requirements
     │
     ▼
Prepare Build Environment
     │
     ├──────────────┬───────────────┐
     ▼              ▼               ▼
Linux Kernel      U-Boot          BusyBox
     │              │               │
     ▼              ▼               ▼
Kernel Config    U-Boot Config   BusyBox Config
     │              │               │
     ▼              ▼               ▼
Cross Compile    Cross Compile    Build
     │              │               │
     ▼              ▼               ▼
  zImage          u-boot        Install Rootfs
     │                              │
     │                              ▼
     │                         Create Initramfs
     │                              │
     │                              ▼
     └──────────────┬───────────────┘
                    │
                    ▼
             Prepare QEMU
                    │
                    ▼
             Boot Linux Kernel
                    │
                    ▼
               Start /init
                    │
                    ▼
                 BusyBox
                    │
                    ▼
              /bin/sh Shell
                    │
                    ▼
              System Testing
```

---

## 8. Build Environment

The project was developed on Ubuntu Linux using an ARM GNU cross-compilation toolchain.

Install the required packages:

```bash
sudo apt install build-essential \
    git wget curl \
    bison flex \
    libncurses-dev \
    libssl-dev \
    libelf-dev \
    gcc-arm-linux-gnueabihf \
    binutils-arm-linux-gnueabihf \
    qemu-system-arm \
    qemu-utils \
    cpio
```

Verify the ARM cross compiler:

```bash
arm-linux-gnueabihf-gcc --version
```

Verify QEMU:

```bash
qemu-system-arm --version
```

---

## 9. Linux Kernel Build

Enter the kernel source directory:

```bash
cd ~/embedded_lab1/kernel/linux-5.15
```

Set the cross-compilation environment:

```bash
export ARCH=arm
export CROSS_COMPILE=arm-linux-gnueabihf-
```

Configure the kernel:

```bash
make versatile_defconfig
```

Build the kernel:

```bash
make -j$(nproc) zImage dtbs modules
```

The resulting files are:

```text
arch/arm/boot/zImage
arch/arm/boot/dts/vexpress-v2p-ca9.dtb
```

They are copied to:

```text
output/zImage
output/vexpress-v2p-ca9.dtb
```

---

## 10. U-Boot Build

Enter the U-Boot source directory:

```bash
cd ~/embedded_lab1/uboot/u-boot-2022.04
```

Set the cross-compilation environment:

```bash
export ARCH=arm
export CROSS_COMPILE=arm-linux-gnueabihf-
```

Configure U-Boot:

```bash
make vexpress_ca9x4_defconfig
```

Build:

```bash
make -j$(nproc)
```

The resulting U-Boot executable is:

```text
output/u-boot
```

---

## 11. BusyBox and Initramfs

The project uses BusyBox 1.35.0.

The BusyBox configuration is stored at:

```text
configs/busybox.config
```

The installed root filesystem is:

```text
rootfs/initramfs/
```

The initramfs contains:

```text
bin/
dev/
etc/
init
lib/
proc/
root/
sbin/
sys/
tmp/
usr/
```

The final initramfs image is generated using:

```bash
cd ~/embedded_lab1/rootfs/initramfs

find . -print0 | cpio --null -ov --format=newc | gzip -9 \
    > ../../output/initramfs.cpio.gz
```

---

## 12. QEMU Boot

The final system can be started with:

```bash
cd ~/embedded_lab1

qemu-system-arm \
    -M vexpress-a9 \
    -cpu cortex-a9 \
    -m 512M \
    -smp 2 \
    -nographic \
    -kernel output/zImage \
    -dtb output/vexpress-v2p-ca9.dtb \
    -initrd output/initramfs.cpio.gz \
    -append "console=ttyAMA0,115200 rdinit=/init mem=512M"
```

The important boot components are:

```text
zImage
    │
    ├── Linux Kernel 5.15
    │
    ▼
vexpress-v2p-ca9.dtb
    │
    ├── Hardware description
    │
    ▼
initramfs.cpio.gz
    │
    ├── BusyBox userspace
    └── /init
```

---

## 13. System Verification

After successful boot, the system provides:

```text
/ #
```

### Kernel Information

```bash
uname -a
```

Example:

```text
Linux embedded-lab 5.15.0 #4 SMP Mon Sep 14 10:24:45 +07 2026 armv7l GNU/Linux
```

This confirms that the ARMv7 Linux kernel has successfully booted.

---

### Root Filesystem

```bash
ls /
```

Expected:

```text
bin
dev
etc
init
lib
proc
root
sbin
sys
tmp
usr
```

---

### Mounted Filesystems

```bash
mount
```

Example:

```text
rootfs on / type rootfs (rw,size=244928k,nr_inodes=61232)
none on /proc type proc (rw,relatime)
none on /sys type sysfs (rw,relatime)
none on /tmp type tmpfs (rw,relatime)
```

---

### Running Processes

```bash
ps
```

Example:

```text
PID   USER     TIME  COMMAND
1     root     0:00  init
2     root     0:00  [kthreadd]
...
60    root     0:00  -/bin/sh
63    root     0:00  ps
```

---

## 14. Output Artifacts

| File                            | Description                  |
| ------------------------------- | ---------------------------- |
| `output/zImage`                 | ARM Linux kernel image       |
| `output/vexpress-v2p-ca9.dtb`   | Device Tree Blob             |
| `output/u-boot`                 | U-Boot executable            |
| `output/initramfs.cpio.gz`      | Compressed BusyBox initramfs |
| `configs/kernel.config`         | Linux Kernel configuration   |
| `configs/busybox.config`        | BusyBox configuration        |
| `bao_cao/MSSV_Lab01_BaoCao.pdf` | Laboratory report            |

---

## 15. Milestones

The project development history is organized into six milestones:

| Milestone | Commit    | Description                     |
| --------- | --------- | ------------------------------- |
| 1         | `a02c945` | Initialize Embedded Linux Lab 1 |
| 2         | `f69139b` | Build Linux kernel for ARMv7    |
| 3         | `af555d0` | Configure and build U-Boot      |
| 4         | `580ce09` | Build BusyBox initramfs         |
| 5         | `d46ac08` | Boot Linux and BusyBox on QEMU  |
| 6         | `fc1cd13` | Add final Lab 1 report          |

This history documents the development process from project initialization to the final bootable embedded Linux system.

---

## 16. Verification Summary

The final system was successfully verified with:

```text
Architecture     : ARMv7
CPU              : ARM Cortex-A9
Machine          : QEMU vexpress-a9
Kernel           : Linux 5.15
Bootloader       : U-Boot 2022.04
Userspace        : BusyBox 1.35.0
Root filesystem   : initramfs
RAM              : 512 MB
SMP              : 2 CPUs
Console          : ttyAMA0
```

Verified functionality:

```text
✓ Linux kernel boot
✓ ARMv7 architecture
✓ Device Tree loading
✓ Initramfs mounting
✓ /init execution
✓ BusyBox startup
✓ Interactive shell
✓ /proc filesystem
✓ /sys filesystem
✓ Process management
✓ Basic userspace utilities
```

---

## 17. Learning Outcomes

This laboratory provided practical experience with:

1. ARM cross-compilation.
2. Linux Kernel configuration and compilation.
3. Device Tree usage.
4. U-Boot configuration and compilation.
5. BusyBox-based embedded userspace.
6. Initramfs construction.
7. Embedded Linux boot flow.
8. QEMU ARM system emulation.
9. Linux filesystem and process management.
10. Git milestone-based project management.

---

## 18. Related Work

Embedded Linux Lab 2 extends this environment with kernel module and driver development.

The Lab 2 work is maintained separately to preserve the Lab 1 baseline.

Planned Lab 2 topics include:

* Linux character device driver
* Kernel module development
* `/dev/lab2`
* `/proc/lab2_info`
* Sysfs attributes
* QEMU driver testing
* NAND simulator
* JFFS2 filesystem

---

## 19. Author

**Embedded Linux Lab 1**

Student: `Ho Ten Sinh Vien`
Student ID: `MSSV`
University: **FPT University**
Major: **IC Design**

---

## 20. License

This repository is an academic laboratory project created for educational purposes.

The project uses open-source components including Linux Kernel, U-Boot, BusyBox, and QEMU. The original licenses of these components remain applicable to their respective source code and distributions.
