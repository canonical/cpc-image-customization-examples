Vendor Data via Image Modification
==================================

If your cloud environment does not have a vendor data capability, then
you can approximate the results by writing configuration files out to
the image before it is registered/uploaded to your cloud.

To do so, we simply use ``mount-image-callback`` to write our
configuration out to a file that ``cloud-init`` will read.  (If you
don't know what ``mount-image-callback`` is, read
:ref:`basic-mount-image-callback-usage` first.)

In addition to reading metadata sources for configuration,
``cloud-init`` will also use the configuration in
``/etc/cloud/cloud.cfg`` and files under ``/etc/cloud/cloud.cfg.d/``.
So we just write our configuration out there::

    $ sudo mount-image-callback xenial.img -- chroot _MOUNTPOINT_ tee /etc/cloud/cloud.cfg.d/99_vendor.cfg << EOF
    #cloud-config
    user: default-user
    EOF

When it runs, ``cloud-init`` will discover this configuration and use
it to customize the system.  For more customization options via
``cloud-init``, see :doc:`pre-boot`.
