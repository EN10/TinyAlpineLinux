# TinyAlpineLinux

TinyAlpineLinux is an experimental, bootable Alpine Linux image focused on a very small footprint. The repository combines a kernel image, an Alpine 3.23 mini root filesystem packed as an initramfs, and a minimal `/init` script that mounts the required pseudo-filesystems and drops into a shell.

This project follows the same lightweight, bootable Linux exploration as https://github.com/EN10/TinyBoxLinux.

## What This Repository Contains

- `bzImage` - prebuilt Linux kernel image
- `initramfs.cpio.gz` - prebuilt initramfs based on Alpine Linux 3.23.4 minirootfs
- `init` - minimal init script used inside the initramfs
- `run.bat` - Windows helper for launching QEMU with the bundled kernel and initramfs

## What `/init` Does

The included `init` script keeps boot logic intentionally small:

1. Mounts `devtmpfs` on `/dev`
2. Mounts `proc` on `/proc`
3. Mounts `sysfs` on `/sys`
4. Creates and mounts `/dev/pts`
5. Rebinds standard input, output, and error to `/dev/console`
6. Starts an interactive login shell with `sh -l`

That makes the image useful as a tiny starting point for initramfs experiments, debugging, and custom appliance-style builds.

## Upstream Base

This project starts from the Alpine Linux mini root filesystem:

- `https://dl-cdn.alpinelinux.org/alpine/v3.23/releases/x86_64/alpine-minirootfs-3.23.4-x86_64.tar.gz`

Release downloads are listed on the Alpine Linux website:

- https://www.alpinelinux.org/downloads

## Rebuilding The Initramfs

Use these steps if you want to reproduce or customize `initramfs.cpio.gz`.

1. Download the Alpine mini root filesystem archive.
2. Extract it into a working directory.
3. Copy this repository's `init` file to the root of the extracted filesystem.
4. Make sure the script is executable:

   ```sh
   chmod +x init
   ```

5. From the root of the extracted filesystem, repack the initramfs:

   ```sh
   find . -print0 | cpio --null -o -H newc | gzip -9 > ../initramfs.cpio.gz
   ```

6. Boot the rebuilt image with the provided kernel or your own kernel.

## Booting With QEMU

### Windows

If `qemu-system-x86_64.exe` is in the same directory as `run.bat`, you can start the image with:

```bat
run.bat
```

Equivalent direct command:

```bat
.\qemu-system-x86_64 -kernel .\bzImage -initrd .\initramfs.cpio.gz
```

### Linux

If QEMU is available in your `PATH`, boot with:

```sh
qemu-system-x86_64 -kernel ./bzImage -initrd ./initramfs.cpio.gz
```

## Intended Use

- Tiny bootable Alpine base for experimentation
- Minimal initramfs environment for QEMU testing
- Starting point for custom embedded, appliance, or recovery images
- Small foundation for container or chroot preparation workflows

## Notes

- This is an experimental minimal system, not a full general-purpose Linux distribution.
- QEMU is not included in the repository.
- The repository is best treated as a small base image that you extend for your own needs.
