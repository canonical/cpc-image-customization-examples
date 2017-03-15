Pre-Boot Customization
======================

Pre-boot customization is performed using the ``mount-image-callback``
tool.  This is in the Ubuntu archive in the cloud-image-utils package,
so can be installed like so::

    sudo apt install cloud-image-utils

Alternatively, it can be found in the upstream bzr branch at
``lp:cloud-utils``.

Basic ``mount-image-callback`` Usage
-------------------------------------

``mount-image-callback`` mounts an image in a temporary directory,
executes a command and then unmounts it.  We will mostly use ``chroot
_MOUNTPOINT_``, which allows us to run commands as if the mounted image
is our root (``mount-image-callback`` will substitute the path to the
mounted image for ``_MOUNTPOINT_``).  This is the primary mechanism we
will use to perform pre-boot customization.

The below snippet fetches the latest xenial release image from
http://cloud-images.ubuntu.com to ``xenial.img`` and uses
``chroot _MOUNTPOINT_`` to run a command within that image::

    $ wget -q http://cloud-images.ubuntu.com/releases/xenial/release/ubuntu-16.04-server-cloudimg-amd64-disk1.img -O xenial.img
    $ sudo mount-image-callback xenial.img -- chroot _MOUNTPOINT_ cat /etc/cloud/build.info
    build_name: server
    serial: 20170311

.. note::
    All of the below examples will assume that you have downloaded a
    xenial release image to ``xenial.img`` locally.  However, these
    instructions should work for all Ubuntu releases.

Changing the Kernel
-------------------

There are different kernels available for each version of Ubuntu.  All
versions have different flavors (e.g. virtual, generic), and LTS
releases also have the choice of the release kernel or the HWE kernel
series.  You can read more about HWE kernels `here
<https://wiki.ubuntu.com/Kernel/LTSEnablementStack>`_.

Cloud images ship with the virtual kernel flavor installed.  If you
wanted to modify the image to include the generic kernel, you would
install the ``linux-generic`` package.  However, as the kernel
installation process is quite involved, simply installing the package
will not result in a bootable image.

First, you need to set up the image so that it won't probe the
running operating system to discover grub boot entries (if you don't do
this then all instances in your cloud will have entries for your
laptop's disks!)::

    $ sudo mount-image-callback xenial.img -- chroot _MOUNTPOINT_ mv /etc/grub.d/30_os-prober /tmp

Once you've done this, you can install the generic kernel::

    $ sudo mount-image-callback --system-resolvconf xenial.img -- chroot _MOUNTPOINT_ apt update
    $ sudo mount-image-callback --system-resolvconf --system-mounts xenial.img -- chroot _MOUNTPOINT_ apt install -y linux-generic

This, however, will leave the virtual kernel installed in the image, so
you will probably also want to uninstall it::

    $ sudo mount-image-callback --system-resolvconf xenial.img -- chroot _MOUNTPOINT_ apt purge -y linux-virtual
    $ sudo mount-image-callback --system-resolvconf xenial.img -- chroot _MOUNTPOINT_ apt autoremove -y

Once you've got all the kernels installed, you can undo our earlier
workaround::

    $ sudo mount-image-callback xenial.img -- chroot _MOUNTPOINT_ mv /tmp/30_os-prober /etc/grub.d

There's one last, important thing to do, however.  When you installed
the kernel, grub will have detected the UUID of the disk image you are
modifying, and used it in ``/etc/grub/grub.cfg``.  We need to revert
that change so that grub will use the label of the disk to boot::

    $ sudo mount-image-callback xenial.img -- chroot _MOUNTPOINT_ sed -i -e "s,root=[^ ]\+,root=LABEL=cloudimg-rootfs," /boot/grub/grub.cfg

Once you've completed all of these steps, you'll have an image with a
different kernel installed, ready to register in your cloud.
