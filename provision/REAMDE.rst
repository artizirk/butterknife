Butterknife provisioning image
==============================

We're using Buildroot to generate all-in-one (<15MB) provisioning image
which can be either booted via PXE or from a USB memory stick.

Steps to reproduce the image:

.. code:: bash

    wget -c http://buildroot.uclibc.org/downloads/buildroot-2015.02.tar.bz2
    tar xvjf buildroot-2015.02.tar.bz2
    cd buildroot-2015.02
    patch -p1 < path/to/butterknife/buildroot/patches/ms-sys.diff
    patch -p1 < path/to/butterknife/buildroot/patches/isohybrid.diff

Bump syslinux version to 6.03 and remove the patch.

To tweak the build:

.. code:: bash

    make menuconfig
    make linux-menuconfig
    make busybox-menuconfig

Enable:

.. code::

    Toolchain -> Enable WCHAR support
    Toolchain -> Enable large file (files > 2 GB) support
    Kernel
        Linux Kernel
            Kernel Version (Same as toolchain headers)
            Kernel configuration (Using a custom config file) -> path/to/butterknife/buildroot/kernel-i386.config
    Filesystem and flash utilities ->
        ms-sys
        btrfs-progs
        ntfs-3g
            ntfsprogs
    Development tools
        jq
    System tools 
        util-linux
            install utilities
    Filesystem images
        Compression method -> xz
        initial RAM filesystem linked into linux kernel
        iso image
    Hardware handling
        parted
        pciutils
    Libraries
        Crypto
            CA Certificates
            openssl
        Networking
            libcurl
                curl binary
    Shell utilities
        dialog
    System configuration
        Init system -> none
        Root filesystem overlay directories -> path/to/butterknife/buildroot/overlay
    Networking applictions
        ntp
            ntpdate
        udpcast
        udpcast tools selection
            sender
            receiver

Make sure you:

* use fdisk from util-linux, not from busybox.
* enable support for necessary ethernet cards, USB and SATA controllers

To compile the image run:

.. code:: bash

  make

