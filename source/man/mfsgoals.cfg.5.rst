.. _mfsgoals.cfg.5:

***************
mfsgoals.cfg(5)
***************

NAME
====

mfsgoals.cfg - replication goals configuration file

DESCRIPTION
===========

The file *mfsgoals.cfg* contains definitions of the replication goals.

SYNTAX
======

::

  'id' 'name' : $'type' { 'label' ... }
  'id' 'name' : 'label' ...

The *#* character starts comments.

DETAILS
=======

There are 40 replication goals, with 'ids' between 1 and 40, inclusive.
Each file stored on the filesystem refers to some goal id and is
replicated according to the goal currently associated with this id.

By default, goal 1 means one copy on any chunkserver, goal 2 means two
copies on any two chunkservers and so on, until 5 - which is the maximal
default number of copies. The purpose of mfsgoals.cfg is to override this
behavior, when desired. The file is a list of goal definitions, each
consisting of 'id', 'name' and a list of 'labels'. The maximal length
of this list is 40 labels.

'id' indicates the goal id to be redefined. If some files are already
assigned this goal id, their effective goal will change.

'name' is a human readable name used by the user interface tools
(lizardfs-setgoal(1), lizardfs-getgoal(1)). 'name' can consist of up to 32
alphanumeric characters: a-z, A-Z, 0-9, _.

'type' specifies the goal type - currently supported types are:

std
  for each file using this goal and for each label, the system will try to
  maintain a copy of the file on some chunkserver with this label.

xorN
  for each file using this goal, the system will split the file into N+1 parts
  (N ordinary + 1 parity). For reading, any N of the parts are necessary. If
  labels are specified, parts will be kept on chunkservers with these labels.
  Otherwise, default wild card labels will be used. N can be in range from 2 to
  9.

ec(K,M)
  for each file using this goal, the system will split the file into K + M
  parts (K data parts and M parity). For reading, any K of the parts are
  necessary. If labels are specified, parts will be kept on chunkservers with
  these labels. Otherwise, default wild card labels will be used. K can be in
  range from 2 to 32 and M from 1 to 32.

  If the type is unspecified it is assumed to be *std*.

The list of 'labels' is a list of chunkserver labels as defined in
mfschunkserver.cfg(5). 'label' can consist of up to 32 alphanumeric
characters: a-z, A-Z, 0-9, _.

One label may occur multiple times - in such case the system will create
one copy per each occurrence.

The special label _ means "a copy on any chunkserver".

.. note:: changing the definition of a goal in mfsgoals.cfg affects all files which currently use a given goal id.

EXAMPLES
========

Some example goal definitions::

  3 3 : _ _ _   # one of the default goals (three copies anywhere)

  8 not_important_file : _   # only one copy

  11 important_file : _ _

  12 local_copy_on_mars : mars _ # at least one copy in the Martian datacenter

  13 cached_on_ssd : ssd _

  14 very_important_file : _ _ _ _

  15 default_xor3 : $xor3

  16 fast_read : $xor2 { ssd ssd hdd }

  17 xor5 : $xor5 { hdd } # at least one part on hdd

  18 first_ec : $ec(3,1)

  19 ec32_ssd : $ec(3,2) { ssd ssd ssd ssd ssd } # all parts on ssd

  20 ec53_mixed : $ec(5,3) { hdd ssd hdd _ _ _ _ _ } # two parts on hdd and one part on ssd

SNAPSHOT FILES
==============

Snapshots share data with the original file until the file receives
modification and diverges from the snapshotted version.
If some snapshot has different goal than its original file, any shared
data are stored according to the goal with higher 'id' of the two.

REPORTING BUGS
==============

Report bugs to <contact@lizardfs.org>.

COPYRIGHT
=========

Copyright 2008-2009 Gemius SA, 2013-2016 Skytechnology Sp. z o.o.

LizardFS is free software: you can redistribute it and/or modify it under the
terms of the GNU General Public License as published by the Free Software
Foundation, version 3.

LizardFS is distributed in the hope that it will be useful, but WITHOUT ANY
WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR
A PARTICULAR PURPOSE. See the GNU General Public License for more details.

You should have received a copy of the GNU General Public License along with
LizardFS. If not, see <http://www.gnu.org/licenses/>.

SEE ALSO
========

mfsmaster.cfg(5)
