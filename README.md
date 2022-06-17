# nvidia-xswitch
The simple way to run a separate X with the discrete NVIDIA graphic card.
This utility offers the possibility to run a separate X with discrete NVIDIA graphic card with full performance.
This project was inspired by the nvidia-xrun project [Witko/nvidia-xrun](https://github.com/Witko/nvidia-xrun).
The main goal of the nvidia-xswitch project is to make it as simple as possible and to make it work on Debian out of the box.
The original nvidia-xrun utility is mainly designed to run on Arch and requires too much tweaking to make it work on Debian.
On Debian/Sid, nvidia-xswitch works out of the box without any tweaking needed (except if you need to setup a different BusID see # BusID setup).
It might as well run on your version/distribution.



# key differences between nvidia-xswitch and nvidia-xrun
   1. no root required, all operations are done by the user without sudo
   2. no need to blacklist nvidia driver
   3. no need to load any additional modules, just install and run



# usage
   1. install nvidia-xswitch: ./nvidia-xswitch --install
   2. Press Alt+Ctrl+F1 (switch to a free TTY)
   3. login
   5. run: nvidia-xswitch --run application

examples:

        nvidia-xswitch --run glxgears
        nvidia-xswitch --run nvidia-settings

you can also run an application with arguments:

        nvidia-xswitch --run glxgears -fullscreen

to run a game with wine, it is very simple, switch to TTY, cd to the directory containing game.exe and run the game as in the example below:

        cd .wine/drive_c/Program\ Files/GameName
        ls | grep exe (this will give you all the available executables)
        nvidia-xswitch --run wine game.exe

how to test if X started with the discrete NVIDIA GPU:

        nvidia-xswitch --run x-terminal-emulator
   
once the terminal has opened, run:
   
        glxinfo | grep -i opengl
        
   you should see NVIDIA in the output

to exit from X, either close the application or press Ctrl+Alt+Backspace if you set this up as the X terminate key combination (see ### Useful tips section below).

### NOTE: 
If you run an older X server, then use the configuration file stored in the alternative_config directory.
Just replace the nvidia-xswitch.conf in the main directory, with the nvidia-xswitch.conf stored in alternative_config directory and run: 

        ./nvidia-xswitch --remove
        ./nvidia-xswitch --install

If you need a specific configuration, see this page: https://download.nvidia.com/XFree86/Linux-x86_64/367.27/README/randr14.html



# BusID setup
In most cases the BusID provided in nvidia-xswitch.conf suffice and no further configuration is needed.
If you have a different setup then you will have to change the BusID in nvidia-xswitch.conf as follows:

   1. find out the BusID of your NVIDIA GPU:

          lspci | grep -i nvidia | awk '{print $1}'
          
   2. the standard output would be:

          01:00.0
          
   5. change BusID in nvidia-xswitch.conf to the BusID you got from the above command

### NOTE
You have to modify the BusID like this (preferably before installing nvidia-xswitch):
if the output of: lspci | grep -i nvidia | awk '{print $1}' is 01:00.0, then in nvidia-xswitch.conf you have to write it as: 01:00:0 (01:00.0 turns into 01:00:0).
If you want to modify BusID after installing nvidia-xswitch, then you have to change it directly in $HOME/.X11/nvidia-xswitch.conf.



### Useful tips:
If you are on a laptop and want a better touchpad experience, then uncomment the following section in nvidia-xswitch.conf (this enables tap-to-click):
 #Section "InputClass"
 #    Identifier "libinput touchpad catchall"
 #    MatchIsTouchpad "on"
 #    MatchDevicePath "/dev/input/event*"
 #    Driver "libinput"
 #    Option "Tapping" "on"
 #EndSection

In order to quickly terminate the currently running xserver with nvidia-xswitch (if not already used), uncomment the keyboard config section in nvidia-xswitch.conf (this sets the key combination Ctrl+Alt+Backspace to terminate the xserver):
 #Section "InputClass"
 #    Identifier "system-keyboard"
 #    MatchIsKeyboard "on"
 #    Option     "XkbModel" "pc104"
 #    Option     "XkbLayout" "us"
 #    Option     "XkbOptions" "terminate:ctrl_alt_bksp"
 #EndSection

### NOTE:
You have to uncomment the chosen sections before installing nvidia-xswitch.
If you want to use any of the above sections after you installed nvidia-xswitch, then uncomment the desired sections directly in $HOME/.X11/nvidia-xswitch.conf.



## Additional info
   1. For better gaming support with wine, it is adviced to enable i386 architecture:
   
          sudo dpkg --add-architecture i386
          sudo apt update
          sudo apt upgrade

   2. Make sure you have NVIDIA driver properly installed.
    On Debian follow this guide: [wiki.debian.org](https://wiki.debian.org/NvidiaGraphicsDrivers)
    In short, these are the standatd steps (tested on Debian Sid):
    
           sudo nano /etc/apt/sources.list
           
   add contrib non-free as follows:
   
        deb http://deb.debian.org/debian sid main contrib non-free
        deb-src http://deb.debian.org/debian sid main contrib non-free
        
   run:
   
        sudo apt update
        sudo apt install nvidia-detect
        
   run nvidia-detect to get the recommended driver for your card:
   
        nvidia-detect
        
   the usual output is:
   
        ***
        It is recommended to install the
            nvidia-driver
            
   install the recommended driver plus firmware:
   
         sudo apt install nvidia-driver firmware-misc-nonfree
        
   reboot

   3. Make sure you also have the following packages installed (grep, gawk, x11-xserver-utils, mesa-utils, coreutils, optional: x11-apps):
   
          sudo apt install grep gawk x11-xserver-utils mesa-utils coreutils
