# USB driver (GNU/Linux)

Here is presented the developpement of an usb driver for GNU/Linux. This was achived with the help of Nicolas Delanoue (http://perso-laris.univ-angers.fr/~delanoue/).
The idea was to developp a GNU/Linux usb driver for a MCP2515 CAN Bus Monitor Demo Board from Microchip. The linux kernel it was developped and tested on: 3.16.0-43-generic 

Some usefull resources we used:

* The basic usb driver usb-skeleton.c (v4.6) -> https://elixir.bootlin.com/linux/latest/source/drivers/usb/usb-skeleton.c
* The "Linux Kernel Module Programming - USB Device Driver 02" video from SolidusCode -> https://www.youtube.com/watch?v=5IDL070RtoQ
* The "Linux Device Drivers, Third Edition By Jonathan Corbet, Alessandro Rubini, Greg Kroah-Hartman" book, and mostly chapter 03 and chapter 13 -> https://www.oreilly.com/openbook/linuxdrive3/book/ch03.pdf
* The missile launcher driver ml_launcher.c -> https://github.com/mavam/ml-driver/blob/master/ml_driver.c

As we struggled to have a working driver, to help readers that would like to developp their own drivers we also present different versions of the driver we made from the most basic and useless (but understanble) one to the final one (NR Driver). Note that this driver, as we wanted it to be as "simple" as possible (and the fact that we are not driver developpers), does not manage a lot of exceptions and possible errors! Be carefull if you want to use it... We only tested it in simple cases (only one device plugged in, only one programm trying to communicate at the same time...).

Here is a list of the presented files: 

* NR Driver
** nr_driver.c
** Makefile
** nr_driver_script.sh
** nrtest_read.py
** nrtest_write.py
* Step by step
** Minimalist driver
** Finding endpoints
** Read function
** Read/write functions
** Read using urbs

## NR driver

This correspond to the source code of our driver for the device.
* nr_driver.c: the c code of the driver.
* Makefile: the makefile to compile the driver.
* nr_driver_script.sh: A script to compile and load the driver into the kernel. Note that the usbhid driver used to claim our device before our own driver. To avoid this, the script unloads the usbhid driver giving us time to plug our device and then reload the usbhid driver (as in our case it is needed for the mouse and the keyboard...). This is the only solution we have so far...
* nrtest_read.py: A python example program using the driver to read values from the device. Note that this python program needs to be executed as root (using sudo for instance) in order to open the corresponding /dev file (created by the driver while pluggin the device).
* nrtest_write.py: A python example program using the driver to write values to the device. Note that this python program needs to be executed as root (using sudo for instance) in order to open the corresponding /dev file (created by the driver while pluggin the device). Using an oscilloscope it is possible to see the sent CAN data over the CAN bus. 

## Step by step

Developping an usb driver for GNU/Linux must be quite difficult when you do not have driver writting skills... In order to help readers that want to create their own driver, here are the steps we took to be able to write our ouwn.
* driver_minimalist.c: This is a minimalist driver that does nothing (no read neather write functions to communicate with the device). But it is a good starting point to write a more usefull driver.
* driver_endpoints.c: This driver automatically searches the endpoints (in and out) of the device.
* driver_read.c: This driver allows the read function. Note that it does not communicate with the device yet! This is just to test the communication between the user space and the kernel!
* driver_read_write.c: This driver allows the read and write functions. Note that it does not communicate with the device yet! This is just to test the communication between the user space and the kernel!
* driver_read_urb.c: Finally this driver communicates with the device througt URBs (USB Data Block). We are now able to communicate from the user space to the kernel AND from the kernel to the usb device. For the urb writing function, you can check our final driver.
