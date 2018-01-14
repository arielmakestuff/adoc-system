Re-install Windows 10 UEFI boot files
=====================================

The purpose of Boot Configuration Data (BCD) is to record the disk partition
information. When booting the system, the computer is able to find the
location of the Windows system from the disk partition by referring to BCD.

If BCD is missing or corrupted, then the computer will not be able to find the
location of the Windows system an you will not be able to boot Windows.

To fix this issue:

1. Boot from the Windows 10 Installation USB

2. Select `Repair Your Computer`

3. Select `Troubleshoot`

4. Select `Advanced Options`

5. Choose `Command Prompt` from the menu

6. Run the following commands:

.. code-block:: winbatch

   X:\windows\system32>diskpart

   Microsoft DiskPart version 6.2.9200

   Copyright (C) 1999-2012 Microsoft Corporation.
   On Computer: MININT-XXXXXXX

   DISKPART>list vol

7. Find the EFI SYSTEM partition (which should be using the FAT32 file system)
   and assign a drive letter to it. For example, if the EFI system partition
   is listed as Volume 2:

.. code-block:: winbatch

   DISKPART>sel vol 2

   Volume 2 is the selected volume.

   DISKPART>assign letter Z:

   DiskPart successfully assigned the drive letter or mount point.

   DISKPART> exit

   Leaving DiskPart...

8. Run the following command to create the UEFI boot files:

.. code-block:: winbatch

   X:\windows\system32>Bcdboot C:\Windows /s Z: /f UEFI
   Boot files successfully created.

9. Enter `exit` to close the command prompt

10. Restart the system.

