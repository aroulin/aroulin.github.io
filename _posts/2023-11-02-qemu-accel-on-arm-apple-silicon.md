---
layout: post
title:  "QEMU Acceleration on ARM Apple Silicon"
---

QEMU 6.2[^1] added support for hardware-based virtualization on ARM Apple Silicon such as M1 SoCs.
It uses the QEMU HVF[^2] accelerator which in turns leverages the Apple Hypervisor framework.

Here's how to use it to boot some image:

```
qemu-system-aarch64 -m 2048 -smp 4                                                       \
                    -cpu cortex-a57                                                      \
                    -bios QEMU_EFI.fd                                                    \
                    -M virt,accel=hvf,highmem=off                                        \
                    -hda noble-server-cloudimg-arm64.img                                 \
                    -device e1000,netdev=net0 -netdev user,id=net0,hostfwd=tcp::5555-:22 \
                    -nographic                                                           \
```

You will need the QEMU_EFI.fd file, which you can find online[^3]

[^1]: <https://wiki.qemu.org/ChangeLog/6.2>
[^2]: <https://wiki.qemu.org/Features/HVF>
[^3]: <https://releases.linaro.org/components/kernel/uefi-linaro/16.02//release/qemu64/>
