***
 Z
***
.. auth-status-writing/none


.. _zfs:

ZFS
===

   The Z File System, or ZFS, is an advanced file system designed to overcome
   many of the major problems found in previous designs.

   Originally developed at Sun™, ongoing open source ZFS development has moved
   to the `OpenZFS Project <http://www.open-zfs.org>`_ .

   ZFS has three major design goals:

   * Data integrity: All data includes a checksum of the data. When data is
     written, the checksum is calculated and written along with it. When that
     data is later read back, the checksum is calculated again. If the
     checksums do not match, a data error has been detected. ZFS will attempt
     to automatically correct errors when data redundancy is available.

   * Pooled storage: physical storage devices are added to a pool, and storage
     space is allocated from that shared pool. Space is available to all file
     systems, and can be increased by adding new storage devices to the pool.

   * Performance: multiple caching mechanisms provide increased performance.
     ARC is an advanced memory-based read cache. A second level of disk-based
     read cache can be added with L2ARC, and disk-based synchronous write
     cache is available with ZIL.

.. seealso::

   * `A guide to install and use zfs on centos 7 <`http://linoxide.com/tools/guide-install-use-zfs-centos-7/">`_

   * `The Open-ZFS Project <http://www.open-zfs.org/>`_

   * `ZFS Manual in the FreeBSD Handbook <https://www.freebsd.org/doc/handbook/zfs.html>`_

   * The `ZFS On Linux - ZOL <http://zfsonlinux.org/>`_ project supplies
     packages and documentation for every major distro:
     `ZFS On Linux - ZOL <http://zfsonlinux.org/>`_

   * `ZFS in the Ubuntu Wiki <https://wiki.ubuntuusers.de/ZFS_on_Linux/>`_

   * `How to install and use ZFS on Ubuntu and why you'd want to <http://www.howtogeek.com/272220/how-to-install-and-use-zfs-on-ubuntu-and-why-youd-want-to/>`_

   * `An extensive Guide about ZFS on Debian by Aaron Toponce <https://pthree.org/2012/04/17/install-zfs-on-debian-gnulinux/>`_

   * `Performance tuning instructions from the Open-ZFS Project <http://open-zfs.org/wiki/Performance_tuning>`_

   * `ZFS RAIDZ stripe width or: How I learned to stop worrying and love raidZ <https://www.delphix.com/blog/delphix-engineering/zfs-raidz-stripe-width-or-how-i-learned-stop-worrying-and-love-raidz>`_

   * `ZFS Administration Part I VDEVs <https://pthree.org/2012/12/04/zfs-administration-part-i-vdevs/>`_













