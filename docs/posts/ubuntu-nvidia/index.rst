.. title: nvidia & ubuntu 18.04
.. slug: ubuntu-nvidia
.. date: Aug 19, 2018
.. tags: nvidia, ubuntu
.. author: Nicolas Paris
.. link: 
.. description:
.. category: linux



My new gigabyte aero 14 does ship a nvidia GTX1050. I had several problem to
make it working correctly.


Installation from scratch
-------------------------
When installing ubuntu from my usb key it simply froze after setup. I had to
enter in maintenance mode, log as root and the resume to get a VGA screen.
After that I was able to install the nvidia drivers by folowing the steps
`the folowing steps <https://www.linuxbabe.com/ubuntu/install-nvidia-driver-ubuntu-18-04>`_

Turning in nvidia mode
----------------------
The command *sudo prime-select nvidia* followed by a reboot makes use of the
nvidia chipset. As a result, the powertop tool show a use of 15W.

Turning intel mode
------------------
The command *sudo prime-select intel* followed by a reboot makes use of the
intel chipset. However a bug introduced with 18.04 makes both nvidia and intel
running and it uses 20W and worst did not allow to
shutdown/reboot/suspend/hibernate. I ended up by using that
`solution <https://www.linuxbabe.com/ubuntu/install-nvidia-driver-ubuntu-18-04>`_ 
which reduce to 7W. Note that I had to make sure the grub was
*nouveau.modeset=0 nouveau.runpm=0* in the */etc/default/grub* file.  After
installed the *tlp* tool I ended up using 5W.

Note that I tried 
`that other solution <https://bugs.launchpad.net/ubuntu/+source/nvidia-prime/+bug/1765363>`_ but this
resulted in system freeze at reboot.
