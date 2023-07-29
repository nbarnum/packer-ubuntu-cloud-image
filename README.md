# packer-ubuntu-cloud-image

Build Ubuntu virtual machine images using [Packer qemu builder](https://developer.hashicorp.com/packer/plugins/builders/qemu) with the Ubuntu [Cloud Images](https://cloud-images.ubuntu.com/) as the disk image source.
Ubuntu cloud images are conventient to build fast and produce cloud-init friendly images.

## Dependencies

* Install [Hashicorp Packer](https://www.packer.io/)
* Install [Qemu](https://www.qemu.org/download/)
* Install a compatible ISO generation tool (`xorriso`, `mkisofs`, `hdiutil`, or `oscdimg`). See [Packer qemu docs](https://developer.hashicorp.com/packer/plugins/builders/qemu#cd-configuration) for more information.

## Building Images

Update the `scripts/install.sh` script to manage what packages are installed during image provisioning. By default, `qemu-guest-agent` and `docker-ce` are installed.

### Build Default Ubuntu Version

Install required plugins:

```shell
$ packer init ubuntu.pkr.hcl
```

Run the Packer build. By default an Ubuntu 22.04 (jammy) image is built:

```shell
$ packer build ubuntu.pkr.hcl
==> qemu.ubuntu: Retrieving ISO
==> qemu.ubuntu: Trying https://cloud-images.ubuntu.com/jammy/current/jammy-server-cloudimg-amd64.img
...
==> qemu.ubuntu: Gracefully halting virtual machine...
==> qemu.ubuntu: Converting hard drive...
Build 'qemu.ubuntu' finished after 1 minute 56 seconds.

==> Wait completed after 1 minute 56 seconds

==> Builds finished. The artifacts of successful builds are:
--> qemu.ubuntu: VM files in directory: output-jammy
```

Generated images will be in `output-<ubuntu version>/ubuntu-<ubuntu version>.img`.

```shell
$ ls -lh output-jammy
total 962M
-rw-r--r-- 1 user user 963M Feb  5 14:53 ubuntu-jammy.img
```

### Build Specific Ubuntu Version

Pass a packer variable `ubuntu_version` to set the Ubuntu cloud image to use as a source.
See available versions on the [Ubuntu Cloud Images](https://cloud-images.ubuntu.com/) page.

For example, pass `-var ubuntu_version=focal` to build with the Ubuntu 20.04 "focal" image:

```shell
$ packer build -var ubuntu_version=focal ubuntu.pkr.hcl
==> qemu.ubuntu: Retrieving ISO
==> qemu.ubuntu: Trying https://cloud-images.ubuntu.com/focal/current/focal-server-cloudimg-amd64.img
...
==> qemu.ubuntu: Gracefully halting virtual machine...
==> qemu.ubuntu: Converting hard drive...
Build 'qemu.ubuntu' finished after 1 minute 55 seconds.

==> Wait completed after 1 minute 55 seconds

==> Builds finished. The artifacts of successful builds are:
--> qemu.ubuntu: VM files in directory: output-focal
```

## Troubleshooting

Pass or set the environment variable `PACKER_LOG=1` to provide additional debug logging output to help troubleshoot build issues.

```shell
$ PACKER_LOG=1 packer build ubuntu.pkr.hcl
2023/02/05 14:48:44 [INFO] Packer version: 1.8.5 [go1.19.4 linux amd64]
2023/02/05 14:48:44 [TRACE] discovering plugins in /usr/bin
2023/02/05 14:48:44 [TRACE] discovering plugins in /home/user/.config/packer/plugins
2023/02/05 14:48:44 [TRACE] discovering plugins in .
2023/02/05 14:48:44 [INFO] PACKER_CONFIG env var not set; checking the default config file path
...

```
