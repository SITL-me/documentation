.. _lizardfs-geteattr.1:

********************
lizardfs geteattr(1)
********************

NAME
====

lizardfs geteattr, lizardfs seteattr,  lizardfs deleattr - get, set or delete extra attributes

SYNOPSIS
========

::

  lizardfs geteattr [-r] [-n|-h|-H] 'OBJECT'...
  lizardfs seteattr [-r] [-n|-h|-H] -f 'ATTRNAME' [-f 'ATTRNAME' ...] 'OBJECT'...
  lizardfs deleattr [-r] [-n|-h|-H] -f 'ATTRNAME' [-f 'ATTRNAME' ...] 'OBJECT'...

DESCRIPTION
===========

**geteattr**, **seteattr** and **deleattr** tools are used to get, set or
delete some extra attributes. Attributes are described below.

OPTIONS
=======

-r
 Enables recursive mode.

-n, -h, -H
 These options are described in lizardfs(1).

EXTRA ATTRIBUTES
================

*noowner*
  This flag means, that particular object belongs to current user ('uid' and
  'gid' are equal to 'uid' and 'gid' values of accessing process). Only root
  ('uid'=0) sees the real 'uid' and 'gid'.

*noattrcache*
  This flag means, that standard file attributes such as uid, gid, mode,
  length and so on won't be stored in kernel cache.

*noentrycache*
  This flag is similar to above. It prevents directory entries from being
  cached in kernel.

COPYRIGHT
=========

2013-2017 Skytechnology Sp. z o.o.

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

lizardfs(1),lizardfs(7)
