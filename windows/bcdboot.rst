BCDBoot Command-Line Options
============================
:Last Updated: 1/10/2017
:Original URL:
    `https://msdn.microsoft.com/en-us/windows/hardware/commercialize/manufacture/desktop/bcdboot-command-line-options-techref-di`_

BCDBoot is a command-line tool used to configure the boot files on a PC or
device to run the Windows operating system. You can use the tool in the
following scenarios:

* Add boot files to a PC after applying a new Windows image. In a typical
  image-based Windows deployment, use BCDBoot to set up the firmware and
  system partition to boot to your image. To learn more, see Capture and Apply
  Windows, System, and Recovery Partitions.
* Set up the PC to boot to a virtual hard disk (VHD) file that includes a
  Windows image. To learn more, see Boot to VHD (Native Boot): Add a Virtual
  Hard Disk to the Boot Menu.
* Repair the system partition. If the system partition has been corrupted, you
  can use BCDBoot to recreate the system partition files by using new copies
  of these files from the Windows partition.
* Set up or repair the boot menu on a dual-boot PC. If you've installed more
  than one copy of Windows on a PC, you can use BCDBoot to add or repair the
  boot menu.

File Locations
---------------

In Windows and Windows Preinstallation Environment (WinPE)
    %WINDIR%\System32\BCDBoot.exe

In the Windows Assessment and Deployment Kit (Windows ADK)
    C:\Program Files (x86)\Windows Kits\10\Assessment and Deployment Kit\Deployment Tools\amd64\BCDBoot\BCDBoot.exe

Supported operating systems
---------------------------

BCDBoot can copy boot environment files from images of Windows 10, Windows
8.1, Windows 8, Windows 7, Windows Vista, Windows Server 2016 Technical
Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2,
or Windows Server 2008.

How It Works
------------

To configure the system partition, BCDBoot copies a small set of
boot-environment files from the installed Windows image to the system
partition.

BCDBoot can create a Boot Configuration Data (BCD) store on the system partition using the latest version of the Windows files:

* BCDBoot creates a new BCD store and initialize the BCD boot-environment
  files on the system partition, including the Windows Boot Manager, using the
  %WINDIR%\System32\Config\BCD-Template file.
* New in Windows 10: During an upgrade, BCDBoot preserves any other existing
  boot entries, such as debugsettings, when creating the new store. Use the /c
  option to ignore the old settings and start fresh with a new BCD store.
* If there is already a boot entry for this Windows partition, by default,
  BCDBoot erases the old boot entry and its values. Use the /m option to
  retain the values from an existing boot entry when you update the system
  files.
* By default, BCDBoot moves the boot entry for the selected Windows partition
  to the top of the Windows Boot Manager boot order. Use the /d option to
  preserve the existing boot order.

On UEFI PCs, BCDBoot can update the firmware entries in the device’s NVRAM:

* BCDBoot adds a firmware entry in the NVRAM to point to the Windows Boot
  Manager. By default, this entry is placed as the first item in the boot
  list. Use the /p option to preserve the existing UEFI boot order. Use
  /addlast to add it to the bottom of the boot order list.

Command-Line Options
--------------------

The following command-line options are available for BCDBoot.exe.

.. code-block:: winbatch

   BCDBOOT <source> [/l <locale>] [/s <volume-letter> [/f <firmware type>]] [/v] [/m [{OS Loader GUID}]] [/addlast or /p] [/d] [/c]

:Option: <source>
:Description:
    Required. Specifies the location of the Windows directory to use as the
    source for copying boot-environment files.

    The following example initializes the system partition by using BCD files
    from the C:\Windows folder:

    .. code-block:: winbatch

       bcdboot C:\Windows


:Option: /l <locale>
:Description:
    Optional. Specifies the locale. The default is US English (en-us).

    The following example sets the default BCD locale to Japanese:

    .. code-block:: winbatch

       bcdboot C:\Windows /l ja-jp

:Option: /s <volume letter>
:Description:
    Optional. Specifies the volume letter of the system partition. This option
    should not be used in typical deployment scenarios.

    Use this setting to specify a system partition when you are configuring a
    drive that will be booted on another computer, such as a USB flash drive
    or a secondary hard drive.

    UEFI:

    * BCDBoot copies the boot files to either the EFI system partition, or the
      partition specified by the /s option.

      BCDBoot creates the BCD store in the same partition.

      By default, BCDBoot creates a Windows Boot Manager entry in the NVRAM on
      the firmware to identify the boot files on the system partition. If the
      /s option is used, then this entry is not created. Instead, BCDBoot
      relies on the default firmware settings to identify the boot files on
      the system partition. By the UEFI 2.3.1 spec, the default firmware
      settings should open the file: \efi\boot\bootx64.efi in the EFI System
      Partition (ESP).

    BIOS:

    1. BCDBoot copies the boot files to either the active partition on the
       primary hard drive, or the partition specified by the /s option.

    2. BCDBoot creates the BCD store in the same partition.

    The following example copies BCD files from the C:\Windows folder to a
    system partition on a secondary hard drive that will be booted on another
    computer. The system partition on the secondary drive was assigned the
    volume letter S:

    .. code-block:: winbatch

       bcdboot C:\Windows /s S:

    The following example creates boot entries on a USB flash drive with the
    volume letter S, including boot files to support either a UEFI-based or a
    BIOS-based computer:

    .. code-block:: winbatch

       bcdboot C:\Windows /s S: /f ALL

:Option: /f <firmware type>
:Description:
    Optional. Specifies the firmware type. Valid values include UEFI, BIOS,
    and ALL.

    On BIOS/MBR-based systems, the default value is BIOS. This option creates
    the \Boot directory on the system partition and copies all required
    boot-environment files to this directory.

    On UEFI/GPT-based systems, the default value is UEFI. This option creates
    the \Efi\Microsoft\Boot directory and copies all required boot-environment
    files to this directory.

    When you specify the ALL value, BCDBoot creates both the \Boot and the
    \Efi\Microsoft\Boot directories, and copies all required boot-environment
    files for BIOS and UEFI to these directories.

    If you specify the /f option, you must also specify the /s option to
    identify the volume letter of the system partition.

    The following example copies BCD files that support booting on either a
    UEFI-based or a BIOS-based computer from the C:\Windows folder to a USB
    flash drive that was assigned the volume letter S:

    .. code-block:: winbatch

       bcdboot C:\Windows /s S: /f ALL

:Option: /v
:Description:
    Optional. Enables verbose mode. Example:

    .. code-block:: winbatch

       bcdboot C:\Windows /v

:Option: /m [{OS Loader GUID}]
:Description:

    Optional. Merges the values from an existing boot entry into a new boot
    entry.

    By default, this option merges only global objects. If you specify an OS
    Loader GUID, this option merges the loader object in the system template
    to produce a bootable entry.

    The following example merges the operating-system loader in the current
    BCD store that the specified GUID identifies in the new BCD store:

    .. code-block:: winbatch

       bcdboot c:\Windows /m {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}

:Option: /addlast
:Description:
    Optional. Specifies that the Windows Boot Manager firmware entry should be
    added last. The default behavior is to add it first. Cannot be used with
    /p.

    .. code-block:: winbatch

       bcdboot C:\Windows /addlast

:Option: /p
:Description:
    Optional. Specifies that the existing Windows Boot Manager firmware entry
    position should be preserved in the UEFI boot order. If entry does not
    exist, a new entry is added in the first position. Cannot be used with
    /addlast.

    By default, during an upgrade BCDBoot moves the Windows Boot Manager to be
    the first entry in the UEFI boot order.

    .. code-block:: winbatch

       bcdboot C:\Windows /p
       bcdboot C:\Windows /p /d

:Option: /d
:Description:
    Optional. Preserves the existing default operating system entry in the
    {bootmgr} object in Windows Boot Manager.

    .. code-block:: winbatch

       bcdboot C:\Windows /d

:Option: /c
:Description:
    Optional. Specifies that any existing BCD elements should not be migrated.

    New for Windows 10: By default, during an upgrade, BCD elements such as
    debugsettings or flightsigning are preserved.

    .. code-block:: winbatch

       bcdboot C:\Windows /c
