.. _replication:

Configuring Replication Modes
*****************************
.. auth-status-proof1/none

LizardFS supports 3 different modes of replication.

.. note:: All replication settings are based on chunks, not on nodes. So if you
         have lets say, 5 chunk servers in a EC3+1 configuration, chunks will
         spread among them but one set will always be 4 chunks. This way all
         active chunk servers are being used and the data/parity chunks get
         nicely distributed amongst them. For details refer to the
         :ref:`technical_whitepaper`.

In the simplest one, the simple goal setup, you specify how many copies of
every chunk of a file or directory will be copied to how many and optionally
also "which" chunk servers.

Note that the write modus here is: client writes chunk to ONE chunk server and
this chunk server replicates this chunk to the other chunk servers.

The second replication mode is called XOR. It is similar to a classic RAID5
scenario in that you have N data chunks and 1 parity chunk. The maximum number
of data chunks in this case is 9.

The third and most advanced mode of replication is the EC mode which does
advanced erasure coding with a configurable amount of data and parity copies.
In this mode clients write quasi parallel to the chunk servers. The maximum
number of data chunks as well as of parity chunks is 32.

.. note:: To ensure proper repair procedure in case of a broken chunk server,
          make sure to always have one chunk server more than your configured
          goals/XOR/EC requires.

Now that you know what they are, lets start configuring them....


.. _standard_goals:

Standard Goals
==============

Goals configuration is defined in the file mfsgoals.cfg used by the master
server.

Syntax of the mfsgoals.cfg file is::

   	id name : label ...

The # character starts comments.

There can be up to 40 replication goals configured, with ids between 1 and 40
inclusive. Each file stored on the file system refers to some goal id and is
replicated according to the goal currently associated with this id.

By default, goal 1 means one copy on any chunk server, goal 2 means two copies
on any two chunk servers and so on. The purpose of mfsgoals.cfg is to override
this behavior, when desired. The file is a list of goal definitions, each
consisting of an id, a name and a list of labels.

**Id**
  indicates the goal id to be redefined. If some files are already assigned
  this goal id, their effective goal will change.

**Name**
  is a human readable name used by the user interface tools (lizardfs setgoal,
  lizardfs getgoal). A name can consist of up to 32 alphanumeric characters:
  a-z, A-Z, 0-9, _.

**list of labels**
  is a list of chunk server labels as defined in the mfschunkserver.cfg file.
  A label can consist of up to 32 alphanumeric characters: a-z, A-Z, 0-9, _.
  For each file using this goal and for each label, the system will try to
  maintain a copy of the file on some chunk server with this label. One label
  may occur multiple times - in such case the system will create one copy per
  each occurrence. The special label _ means "a copy on any chunk server".

Note that changing the definition of a goal in mfsgoals.cfg affects all files
which currently use a given goal id.

Examples
--------

Example of goal definitions::

   	3 3 : _ _ _ # one of the default goals (three copies anywhere)
   	8 not_important_file : _ # only one copy
   	11 important_file : _ _
   	12 local_copy_on_mars : mars _ # at least one copy in the Martian data center
   	13 cached_on_ssd : ssd _
   	14 very_important_file : _ _ _ _

For further information see::

  $ man mfsgoals.cfg

Show current goal configuration
-------------------------------

From the command line::

   $ lizardfs-admin list-goals <master ip> <master port>

Via cgi (webinterface):

In the ‘Config’ tab there is a table called ‘Goal definitions’.

Set and show the goal of a file/directory
-----------------------------------------

**set**
   The goal of a file/directory can be set using::

	   $ lizardfs setgoal goal_name object

   which will result in setting the goal of an object to goal_name.

   By default all new files created in the directory are created with the
   goal of the directory.

   Additional options are:

   * (-r) - change goals recursively for all objects contained in a directory
   * goal_name[+|-] - when goal_name is appended with +, the goal is changed
     only if it will “increase security” (i.e. goal_name id is higher than id
     of the current goal)


**show**
   Current goal of the file/directory can be shown using::

      $ lizardfs getgoal object

   The result is the name of the currently set goal.

   To list the goals in a directory use::

      $ lizardfs getgoal -r directory

   which for every given directory additionally prints the current value of
   all contained objects (files and directories).

   To show where exactly file chunks are located use::

      $ lizardfs fileinfo file

For further information see: :ref:`lizardfs-getgoal.1`
:ref:`lizardfs-fileinfo.1`



Setting up XOR
==============

Setting XOR goals works in a similar way as setting standard goals. Instead of
just defining *id* *name* *:* *list of labels*, you add between the *:* and
the *list of labels* the xor definition ($xor2 for 2+1, $xor3 for 3+1 ...) and
surround the labels with {}. If you do not specify any labels all chunk servers
known to the system will be used. Each set will be of the size specified by
your XOR definition but writes will be spread over all chunk servers.

For example if you have 5 chunk servers and define $xor2 (2 data and 1 parity
chunk per set), the first set could write to chunk server 1,2 and 3, the second
one to chunk server 2,3,4 and the next to chunk server 1,3,5. The maximum
number of data chunks is currently 9.

Example
-------

This goes into :ref:`mfsgoals.cfg.5` and sets custom goals 15,16 and 17::

  15 default_xor3 : $xor3 # simple xor with 3 data and 1 parity chunks on all
                          # defined chunk servers. Each set will be written to
                          # 4 chunk servers but sets will be load balanced so
                          # all participating chunk servers will be utilized.

  16 fast_read : $xor2 { ssd ssd hdd } # simple xor with 2 data and 1 parity
                                       # utilizing only 2 chunk servers, ssd
                                       # and hdd and writing 2 copies to the
                                       # ssd chunk servers and one to the hdd
                                       # chunk server.

  17 xor5 : $xor5 { hdd _ }            # XOR with 5 data and 1 parity drive
                                       # and at least one part of each set
                                       # written to the chunk server labeled
                                       # hdd

Applying, showing and listing goals works the same way as for
:ref:`standard_goals`.

Setting up EC
=============

EC goals are set nearly the same way as XOR goals. The only difference is that
you specify an ec definition in the format *$ecM,K* , where M stands for the
data parts and K for the parity parts instead of the xor definition. The
maximum size for each of those  (M or K) is 32.

Example
-------

This goes into :ref:`mfsgoals.cfg.5` and sets custom goals 18, 19 and 20::

  18 first_ec : $ec(3,1)
  # Use ec3+1 on all chunk servers. Load will be balanced and each write will
  # consist of 3 data and 1 parity part

  19 ec32_ssd : $ec(3,2) { ssd ssd ssd ssd ssd }
  # Use ec3+2 but write only to chunk servers labeled ssd. Each write will
  # consist of 3 data and 2 parity parts.

  20 ec53_mixed : $ec(5,3) { hdd ssd hdd _ _ _ _ _ }
  # Use ec5+3 on all chunk servers but always write 2 parts to chunk servers
  # labeled hdd and 1 part to chunk servers labeled ssd. The remaining parts go
  # to any chunk server. Each write will have 5 data and 3 parity parts. Load
  # will be distributed amongst all chunk servers except for 2 parts which will
  # be written to chunk servers labeled hdd and 1 part which will be written to
  # chunk server ssd on every write.

As you can see EC is extremely flexible and is also our fastest replication
mode since writes from the client get spread over the chunk servers according
to the goals set.

.. seealso::

   * :ref:`mfsgoals.cfg.5`
   * :ref:`lizardfs-getgoal.1`
   * :ref:`lizardfs.1`
   * :ref:`lizardfs-admin.8`
   * :ref:`labeling_chunkserver`
