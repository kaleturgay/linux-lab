
# i386/pc Usage

## Linux v2.6.21.5

This is based on buildroot/target/device/x86/i686/linux-2.6.21.5.config, with nfs and network feature support.

At first, set ARCH to i386 for all kernel versions <= v2.6.23:

    $ mkdir boards/i386/pc/bsp/kernel/v2.6.21.5
    $ make board-config ARCH=i386 LINUX=v2.6.21.5

Second, Create a missing /dev/null device:

    $ make boot ROOTDEV=/dev/nfs XKCLI=init=/bin/sh
    /bin/sh: can't access tty; job control turned off
    / # mknod dev/null c 1 3
    / # sync
    / # reboot -f

Third, boot it normally:

    $ make boot ROOTDEV=/dev/nfs
    Welcome to Linux Lab
    linux-lab login: root
    #
    # uname -a
    Linux linux-lab 2.6.21.5-dirty #4 Sat Dec 28 17:36:27 UTC 2019 i686 GNU/Linux

Note, to compile old kernel, please switch to older gcc version as following:

    $ make gcc-list
    Listing prebuilt toolchain ...
    [ internal ]:
    Remote.:
    Local..:
    Tool...: gcc
    Version: gcc (Ubuntu/Linaro 4.4.7-8ubuntu1) 4.4.7
    More...: /usr/bin/gcc-4.4 /usr/bin/gcc-4.8 /usr/bin/gcc-8

    $ make gcc-switch CORI=internal GCC=4.4

## Linux v2.6.12.6

For this old kernel version, please make sure apply the prepared patchset:

    $ mkdir boards/i386/pc/bsp/kernel/v2.6.12.6
    $ make board-config ARCH=i386 LINUX=v2.6.12.6
    $ make kernel-checkout
    $ make kernel-patch
    $ make kernel-defconfig
    $ make kernel
    $ make boot
    $ make boot ROOTDEV=/dev/nfs
    $ make boot ROOTDEV=/dev/hda

Note: the second patch for mm/page_alloc.c is not the last solution, it is just a workaround for booting.

## Linux v2.6.11.12

There is no valid 2.6.11 tags currently, create one:

    $ pushd linux-stable
    $ git log remotes/origin/linux-2.6.11.y
    $ git tag v2.6.11.12 8e63197f
    $ popd

    $ make board-config ARCH=i386 LINUX=v2.6.11.12
    $ make kernel-cloneconfig LINUX=v2.6.12.6 LINUX_NEW=v2.6.11.12
    $ make kernel-checkout
    $ make kernel-patch
    $ make kernel-defconfig
    $ make kernel
    $ make boot
    $ make boot ROOTDEV=/dev/nfs
    $ make boot ROOTDEV=/dev/hda