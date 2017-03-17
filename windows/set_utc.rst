UTC in Windows
==============

From the `Arch Linux wiki <https://wiki.archlinux.org/index.php/Time#UTC_in_Windows>`_.

One reason users often set the RTC in localtime is to dual boot with Windows (which uses localtime). However, Windows is able to deal with the RTC being in UTC with a simple registry fix. It is recommended to configure Windows to use UTC, rather than Linux to use localtime.

Using regedit, add a DWORD value with hexadecimal value 1 to the registry:

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\TimeZoneInformation\RealTimeIsUniversal

You can do this from an Administrator Command Prompt running:

    reg add "HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\TimeZoneInformation" /v RealTimeIsUniversal /d 1 /t REG_DWORD /f

Alternatively, create a \*.reg file with the following content and
double-click it to import it into registry:

    Windows Registry Editor Version 5.00

    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\TimeZoneInformation]
        "RealTimeIsUniversal"=dword:00000001

If the above appears to have no effect, and a 64-bit variant of Windows is
being used, using a QWORD value instead of a DWORD value may resolve the
issue.

Should Windows ask to update the clock due to DST changes, let it. It will
leave the clock in UTC as expected, only connecting the displayed time.

The hardware clock and system clock time may need to be updated after setting
this value.

If you are having issues with the offset of the time, try reinstalling tzdata
and then setting your time zone again:

    # timedatectl set-timezone America/Los_Angeles
