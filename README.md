ECONET FILESERVER TIME UTILITY
==============================

This utility for the BBC Micro and Master will fetch the time and date from an
Econet fileserver and display it in the same format as the *TIME command on a
BBC Master (e.g. "Sat,19 Nov 2022.13:40:15").

The fetch is done by OSWORD &14 [communicate with fileserver], function 16
[read date and time]) provided by the Econet NFS/ANFS ROM.

When supplied with the option "S" or "I", it will also attempt to set the
time on the local machine using either the OSWORD &0F call (which works on the
BBC Master to set the battery-backed CMOS clock) or using the [e.g.] "*DATE
=19/11/2022" and "*TIME =13:40:15" commands required to set the battery-backed
clock on an IntegraB module for the BBC Micro.

Reading the clock supports the 'date hack' to extend the original range of
years from the 4-bit offset from 1981 (up to 1996) by using the top 3 bits of
the 'day' field for years up to 2099.

The utility has been tested against PiEconetBridge and an Acorn Level 3
fileserver running patched v1.25 code to support the date hack.

If you are not logged onto the fileserver, the error "Fileserver time not
available (logged on?)" will be displayed.  If the fileserver is not found
on the network, standard errors will be displayed by NFS/ANFS.


Bugs
----

The year code doesn't handle dates beyond 2099, even though the 7-bit range
extends the supported years up to 2108 - dates beyond this are often not
supported by the [patched] Master MOS and IntegraB IBOS, anyway).  If this
ever becomes and issue, someone else will have to solve it!

There doesn't appear to be a way to tell is the OSWORD &0F call has succeeded,
other than checking the subsequent time with *TIME, so no error will be
displayed, if this fails.

If the "I" option is used on a computer without an IntegraB IBOS, strange
output or errors may be displayed, depending on the support for the *DATE and
*TIME commands (the Master will typically run *DATE from the Econet
LIBRARY directory and then the MOS *TIME command, for example).

The time retrieved from a Level 3 fileserver appears to always contain the
seconds as 00, so the time will be rounded down to the nearest minute.  This
may be a limitation of the software or have been caused by the extra space
for the year patch.
