---
layout: post
title:  "QEMU Booting an Ubuntu Cloud Image"
---

To boot a cloud image, you need to setup SSH keypair access with a cloud-init medium (e.g., cdrom).

Use this QEMU script (adapt for Linux if needed):
[provision_ubuntu2404_qemu_macosx.sh](https://gist.github.com/aroulin/a17d1123bdbfc755906dcf81bfc48c61)

You will need to download separately the QEMU_EFI.fd file[^1].

And then login with

    ssh root@localhost -p 5555 -o "IdentitiesOnly=yes" -i /tmp/ubuntuqemuboot/cloudinitmetadata/id_rsa_ubuntu2404boot

[^1]: <https://releases.linaro.org/components/kernel/uefi-linaro/16.02//release/qemu64/>
