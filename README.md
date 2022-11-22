ECONET FILESERVER TIME UTILITY
==============================

This utility for the [BBC Micro](https://en.wikipedia.org/wiki/BBC_Micro) and
Master will fetch the time and date from an Econet fileserver and display it
in the same format as the `*TIME` command on a BBC Master (e.g. `Sat,19 Nov
2022.13:40:15`).

The fetch is done by [OSWORD &14](https://beebwiki.mdfs.net/OSWORD_%2614)
(communicate with fileserver), function 16 (read date and time) provided by
the Econet NFS/ANFS ROM.

When run with `S`, it will attempt to set the clock on the local machine to
the time retrieved from the fileserver.  This will be done in one of two ways,
depending on the result of
[OSBYTE &49](https://beebwiki.mdfs.net/OSBYTE_%2649), which identifies an
IntegraB board is present (an upgrade for a Model B with a battery-backed
RTC):

* if an IntegraB is not detected,
[OSWORD &0F](https://beebwiki.mdfs.net/OSWORD_%260F) call will be used, which
will set the CMOS clock on a BBC Master
* if an IntegraB is detected, the clock on that will be set using the [e.g.]
`*DATE =19/11/2022` and `*TIME =13:40:15` OS commands

These modes can be forced using the `I` and `C` options, respectively.

Reading the clock supports the 'date hack' to extend the original range of
years from the 4-bit offset from 1981 (up to 1996) by using the top 3 bits of
the 'day' field for years up to 2099.

The utility has been tested against
[PiEconetBridge](https://github.com/cr12925/PiEconetBridge) and an Acorn Level
3 fileserver running patched v1.25 code to support the date hack.

If you are not logged onto the fileserver, the error `Fileserver time not
available (logged on?)` will be displayed.  If the fileserver is not found
on the network, standard errors will be displayed by NFS/ANFS.


Implementation
--------------

The utility is in 6502 machine code assembled by the supplied BBC BASIC
program.

The BASIC program is supplied in tokenised form and as a text file which can
be `*EXEC`d to create.  This is to enable changes to be tracked by git.

The code is set to load at &FFFF2C00 and is under &400 in size so should load
below the screen memory area, regardless of display MODE.  On a Tube system,
the code will load and run on the I/O processor, regardless of Tube processor
type.


Bugs
----

The year code doesn't handle dates beyond 2099, even though the 7-bit range
extends the supported years up to 2108 - dates beyond this are often not
supported by the [patched] Master MOS and IntegraB IBOS, anyway).  If this
ever becomes an issue, someone else will have to solve it!

There doesn't appear to be a way to tell if the OSWORD &0F call has succeeded,
other than checking the subsequent time with *TIME, so no error will be
displayed, if this fails.

If the `I` option is used on a computer without an IntegraB IBOS, strange
output or errors may be displayed, depending on the support for the `*DATE`
and `*TIME` commands (a BBC Master will typically run `*DATE` from the Econet
Library directory and then the MOS `*TIME` command, for example).

The time retrieved from a Level 3 fileserver appears to always contain the
seconds as 00, so the time will be rounded down to the nearest minute.  This
may be a limitation of the software or have been caused by the extra space
required for the 7-bit year patch.
