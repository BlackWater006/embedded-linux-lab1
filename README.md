# Embedded Linux Lab 1

## 1. Lab Overview

This project implements a basic Embedded Linux system for the ARM Versatile Express Cortex-A9 platform.

The system consists of:

- Linux Kernel 5.15
- U-Boot 2022.04
- BusyBox 1.35.0
- ARM GNU cross-compilation toolchain
- QEMU ARM Versatile Express emulation

## 2. Project Structure

```text
embedded_lab1/
├── configs/
│   ├── kernel.config
│   └── busybox.config
├── output/
│   ├── zImage
│   ├── vexpress-v2p-ca9.dtb
│   ├── u-boot
│   └── initramfs.cpio.gz
├── rootfs/
│   └── initramfs/
├── bao_cao/
└── README.md
