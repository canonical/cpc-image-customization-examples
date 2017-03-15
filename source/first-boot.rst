First-Boot Customization
========================

First-boot customization is performed using ``cloud-init``.
``cloud-init`` runs during boot of all Ubuntu instances, discovers the
environment in which it is running and configures the instance
accordingly.  As well as configuring networking and user access, it can
also perform customization based on instructions provided by the cloud
vendor (vendor data) and by users (user data).  We are specifically
interested in vendor-data.

Vendor Data
-----------

Configuring Vendor Data
~~~~~~~~~~~~~~~~~~~~~~~

Vendor data is presented to the instance as a plain text file.  How
this file is configured depends on the implementation of your cloud,
so you will need to determine how to configure it for your cloud.  For
the remainder of this document, the examples will be the text file that
you should configure as vendor data.

.. note::
    For cloud implementations that don't support vendor-data from the
    metadata service, the image can be modified to provide a similar
    effect.  See :doc:`vendor-data-via-modification` for more details
    on how this can be achieved.

Testing Vendor Data
~~~~~~~~~~~~~~~~~~~

``cloud-init`` parses and process vendor-data and user-data in almost
exactly the same way.  This means that you can easily test vendor-data
that you're writing by passing it to an instance you're launching as
user-data.

Using an Alternative Default User
---------------------------------

The Ubuntu cloud images ship with the ``ubuntu`` user configured as
their default.  In general, Ubuntu users expect this user to exist, so
changing away from it is discouraged.  However, if you still wish to
do so, you can configure this with the following vendor-data:

.. literalinclude:: /../vendor-data/alternative-default-user.txt
