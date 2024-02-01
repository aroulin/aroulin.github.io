---
layout: post
title:  "Using Virtme to boot a custom kernel and modules on a Linux VM on ARM Apple Sillicon"
---

Refer to the two previous posts to boot an Ubuntu Linux VM on ARM Apple Sillicon:
- [QEMU Acceleration on ARM Apple Silicon](https://aroulin.github.io/2023/11/02/qemu-accel-on-arm-apple-silicon.html)
- [QEMU Booting an Ubuntu Cloud Image](https://aroulin.github.io/2023/11/02/qemu-booting-ubuntu-cloud-img.html)

From here on, we have an Ubuntu GNU/Linux VM we can login.

The two next steps are:
1. build a Linux kernel and modules
2. Boot that new kernel with the modules


## Build a custom Linux kernel and modules

Install dependencies to build the kernel:

```
apt update
sudo apt install libncurses-dev gawk flex bison openssl libssl-dev dkms libelf-dev libudev-dev libpci-dev libiberty-dev autoconf llvm
```

Install virtme
```
apt install python3-setuptools
git clone https://github.com/ezequielgarcia/virtme.git
cd virtme/
sudo ./setup.py install
```

Build the kernel and modules

```
wget https://git.kernel.org/torvalds/t/linux-6.8-rc2.tar.gz
tar xvf linux-6.8-rc2.tar.gz

cd linux-6.8-rc2/

# Generate minimal virtme kernel configuration
virtme-configkernel --defconfig

# Build kernel and modules
make -j4

# Install modules to tmp directory
mkdir tmp
make INSTALL_MOD_PATH=./tmp/ modules_install
```

## Boot the new kernel with the modules

This step is made super easy using virtme. You will boot the new kernel with the modules and even have access to the
host file system.

```
virtme-run --kimg arch/arm64/boot/Image.gz --mdir tmp/lib/modules/6.8.0-rc2
```

The kernel will immediately boot:
```
Could not access KVM kernel module: No such file or directory
qemu-system-aarch64: failed to initialize kvm: No such file or directory
qemu-system-aarch64: falling back to tcg
[    0.000000] Booting Linux on physical CPU 0x0000000000 [0x411fd070]
[    0.000000] Linux version 6.8.0-rc2 (root@circle-the-wagons) (gcc (Ubuntu 13.2.0-13ubuntu1) 13.2.0, GNU ld (GNU Binutils for Ubuntu) 2.42) #2 SMP PREEMPT Thu Feb  1 17:13:35 UTC 2024
[    0.000000] KASLR enabled
[    0.000000] random: crng init done
[    0.000000] Machine model: linux,dummy-virt
[    0.000000] efi: UEFI not found.
[    0.000000] NUMA: No NUMA configuration found
....
Starting systemd-udevd version 253.5-1ubuntu7[    4.592842] virtme-init: triggering udev coldplug
[   12.677417] virtme-init: waiting for udev to settle
[   26.858694] virtme-init: udev is done

virtme-init: console is ttyAMA0
root@(none):/#
```

Interesting to not that KVM was not installed in this Linux VM so instead of nested virtualization, QEMU fell back to TCG (emulation).
