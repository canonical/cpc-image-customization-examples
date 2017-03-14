Methods of Customization
========================

Customization of Ubuntu images can happen at the following times:

- before boot,
- at first boot, or
- after boot.

Before Boot
-----------

Pre-boot customizations take the form of modifying the image as
downloaded from http://cloud-images.ubuntu.com before
uploading/registering it in your cloud.  As pre-boot customization
requires vendor actions for each new image, it is generally preferable
that customization happen as first boot (see below).  That said, there
are some customizations that can only be performed before the image
boots.

See :doc:`pre-boot` for examples of when you might want to customize an
image before boot, and how you go about doing it.

At First Boot
-------------

Customizations at first boot are performed by `cloud-init
<https://cloudinit.readthedocs.io/en/latest/>`_, which runs during the
boot process in Ubuntu for precisely this purpose.  This is the
preferred way of performing customization, as it generally only
requires one-time configuration by the vendor and is more transparent
to users.

See :doc:`first-boot` for examples of how to customize Ubuntu images at
boot time.

After Boot
----------

If the customization that you want to perform on images is only
required for specific users and/or specific use cases, then you might
want to consider providing your customizations in a package
[#packages]_ which can be installed by users as and when they are
required.

This isn't, strictly speaking, image customization, but you may find
that it is the simplest route to achieve your goals.


.. [#packages]
    We would recommend either .deb packages or `snap packages
    <https://snapcraft.io/>`_, as these are well-understood by Ubuntu
    users, and well-documented.
