# Pause---Restart-Linux-Module

We have divided this project into two parts :

1)The Process Management Part:

The process management part is used for the pause-restore mechanism which is implemented on linux terminal with the help of a tool called CRIU ("Checkpoint/Restore In Userspace"). This mechanism is tested on a program "test.cpp". 
This test program asks for two inputs from a user. We give one input, pause the process and save its current state on our working directory.
Then we copy our directory to usb drive with the help of device driver, and then we switch to another machine.
On the other machine, we copy the directory and make this as our working directory.
At last, we restore the process from its saved state, and give the next input to the program.
We see that the first machine's input as well as second machine's input are displayed correctly.

The procedure is:

1)Compile the "test.cpp" with the command :

  g++ test.cpp -o test
  
2)Run the program by :

  ./test
  
3)Give one input, open another terminal and give the commands as shown below.

3)Get the process pid by :

  ps aux
  
4)Pause and save the process state(dump) by :

  sudo criu dump -t <pid> --shell-job
  
  (You will see that the program gets killed and the process state is saved in our directory.)
  
5)Copy the directory on the usb drive.

6)On the other machine, copy this directory from usb, and give the command (while working in this directory only) :

  sudo criu restore --shell-job
  
  (You will see that the program asks for another input, hence we successfully restored the process.)
.
.
.
.
.
.

  
2)The USB device driver:

The usual steps for  Linux usbdevice driver may be repeated with the above code, along with the pen drive steps

1. Build the driver (pen_driver.ko) by running make.
2. Load the driver using insmod pen_driver.ko.
3. Plug in the pen drive (after making sure that the usb-storage driver is not already loaded).
4. Check for the dynamic creation of /dev/pen0 (0 being the minor number obtained — check dmesg logs for the value on your system).
5. Possibly try some write/read on /dev/pen0 (you most likely will get a connection timeout and/or broken pipe errors, because of 
   non-conforming SCSI commands).
6. Unplug the pen drive and look for /dev/pen0 to be gone.
7. Unload the driver using rmmod pen_driver.


Notes

1. Make sure that you replace the vendor id & device id in the above code examples by the ones of your pen drive. Also, make sure that 
   the endpoint numbers used in the above code examples match the endpoint numbers of your pen drive. Otherwise, you may get an error like “… bulk message returned error 22 – Invalid argument …”, while reading from pen device.

2. Also, make sure that the driver from the previous article is unloaded, i.e. pen_info is not loaded. Otherwise, it may give an error 
   message “insmod: error inserting ‘pen_driver.ko’: -1 Device or resource busy”, while doing insmod pen_driver.ko.

3. One may wonder, as how does the usb-storage get autoloaded. The answer lies in the module autoload rules written down in the file 
   /lib/modules/<kernel_version>/modules.usbmap. If you are an expert, you may comment out the corresponding line, for it to not get autoloaded. And uncomment it back, once you are done with your experiments.

4. In latest distros, you may not find the detailed description of the USB devices using cat /proc/bus/usb/devices, as the /proc/bus/usb/ 
   itself has been deprecated. You can find the same detailed info using cat /sys/kernel/debug/usb/devices – though you may need root permissions for the same. Also, if you do not see any file under /sys/kernel/debug (even as root), then you may have to first mount the debug filesystem, as follows: mount -t debugfs none /sys/kernel/debug.
