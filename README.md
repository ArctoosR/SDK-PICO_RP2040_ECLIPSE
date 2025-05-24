# SDK-PICO_RP2040_ECLIPSE


https://hamlab.net/mcu/raspbery_pi_pico_rp2040.html

https://blog.naver.com/PostList.naver?blogId=chcbaram&from=postList&categoryNo=27



cmake -G "Ninja" -DCMAKE_BUILD_TYPE=Debug -S ../pico-examples -B build

//*******************************************************************************************
1:

git clone https://github.com/raspberrypi/pico-sdk.git

cd pico-sdk

git submodule update --init


در پوشه پروژه 
2:
ساخت فایل CmakeLists.txt  در پوشه اصلی
cmake_minimum_required(VERSION 3.13)

include(${PICO_SDK_PATH}/external/pico_sdk_import.cmake)


#include(pico_sdk_import.cmake)

project(Test_Ninja_Sample)

pico_sdk_init()

add_executable(Test_Ninja_Sample src/main.c)

target_link_libraries(Test_Ninja_Sample pico_stdlib)

pico_add_extra_outputs(Test_Ninja_Sample)

set(CMAKE_ASM_COMPILER "C:/ToolChain_Arm/12.3 rel1/bin/arm-none-eabi-gcc.exe")


3:

cmake -G "Ninja" -DCMAKE_BUILD_TYPE=Debug -DPICO_SDK_PATH=./pico-sdk -S . -B build

4:
ساخت و اجرای پروژه
cmake --build build



ninja -C build




 5:
 
 my_pico_project/  
│── CMakeLists.txt      ← این فایل را اینجا قرار دهید  
│── pico-sdk/           ← مسیر Pico SDK  
│── src/                ← فایل‌های سورس پروژه  
│   ├── main.c          ← فایل اصلی برنامه  
│── build/              ← مسیر پیشنهادی برای خروجی‌های ساخت  


//****************************************************************************************
###https://shawnhymel.com/2168/how-to-build-openocd-and-picotool-for-the-raspberry-pi-pico-on-windows/##
Install Git for Windows SDK
Git for Windows SDK is different than Git for Windows, as the former contains a number of tools that help us build programs on Windows (including a package manager). Regular Git Bash does not have these features.

Head to https://gitforwindows.org/#download-sdk and download Git for Windows SDK. Download the latest installer for your version of Windows (likely the 64-bit version).

Run the installer, accepting all the defaults. This will open a command prompt window and begin to download/install Git for Windows SDK. It will take some time, as it’s a rather large set of files.

1: Download [https://gitforwindows.org/#download-sdk]


2:
Note that Git for Windows SDK installs (by default) in C:\git-sdk-64. This directory contains everything as part of the SDK, which means you can delete the directory when you are done using it.

Install Packages
One of the best features of Git for Windows SDK is the pacman package manager. It allows you to install libraries and software similar to how you might do it in Linux.

Run C:\git-sdk-64\git-bash.exe. Note that you must use this executable to run Git for Windows SDK and not the one you may have installed in the previous tutorial! The window should say “SDK-64:/” at the top if you’re running the correct version.

In the terminal, enter the following commands:

pacman -Syu 

pacman -Su

pacman -S mingw-w64-x86_64-toolchain git make libtool pkg-config autoconf automake texinfo wget

When pacman asks which members to install in the mingw-w64-x86_64-toolchain, press ‘enter’ to install all.


3:


When asked “pkg-config and pkgconf are in conflict. Remove pkgconf? [Y/n],” enter Y.

When asked “Proceed with installation? [Y/n],” enter Y again.

This will take some time to download and install all of the packages.

Note: there is an issue with libusb-1.0.24 on MSYS2. We will manually install v1.0.23 to prevent the segmentation fault from occurring when running OpenOCD or picotool. If you run pacman -Su to update packages after this manual installation process, it will remove version 1.0.23 and update libusb to the latest (1.0.24 at the time of writing). For more information, see this forum post.

Enter the following commands to install libusb-1.0.23:

cd ~/Downloads

wget http://repo.msys2.org/mingw/x86_64/mingw-w64-x86_64-libusb-1.0.23-1-any.pkg.tar.xz
edit:[wget http://repo.msys2.org/mingw/x86_64/mingw-w64-x86_64-libusb-1.0.26-1-any.pkg.tar.zst]

pacman -U mingw-w64-x86_64-libusb-1.0.23-1-any.pkg.tar.xz
edit:[pacman -U mingw-w64-x86_64-libusb-1.0.26-1-any.pkg.tar.zst]
Enter Y when asked “Proceed with installation? [Y/n].”

4:

At this point, we’re ready to build our tools!

Build OpenOCD
OpenOCD (Open On-Chip Debugger) is an open-source tool that communicates with GDB to send commands over JTAG or SWD to a number of different microcontrollers. The RP2040 is not officially supported in OpenOCD at the time of writing, so we need to build a special branch of OpenOCD.

In your Git for Windows SDK terminal, enter the following commands:

cd /c/VSARM/sdk/pico

git clone https://github.com/raspberrypi/openocd.git --branch picoprobe --depth=1 

cd openocd

./bootstrap

./configure --enable-picoprobe --disable-werror

make

When OpenOCD is done building, you will need to copy in the libusb-1.0.dll library file from your Git for Windows SDK directory. This .dll file must be in the same directory as your openocd.exe file for the tool to work. If you move openocd.exe, you’ll need to copy libusb-1.0.dll to the same folder.

Enter the following command to copy the file into the current directory:

cp /c/git-sdk-64/mingw64/bin/libusb-1.0.dll src/libusb-1.0.dll

You can test running OpenOCD with the following command:

src/openocd.exe

You should expect it to throw some errors and exit, as we haven’t given it any parameters.

5:


Note: I do not recommend calling make install as shown in the Getting Started guide! This will make the Pico version of OpenOCD the default OpenOCD for your whole system. If you plan to use OpenOCD for other embedded debugging, you’ll want to keep the Pico version separate. For now, just leave the executable in the src folder. You can call this executable explicitly from the command line or set VS Code to use it on a per-project basis.

Build Picotool
Picotool is a program used to inspect RP2040 binaries and interact with the RP2040 in bootloader mode.

Note that to build picotool, you will need to have the SDK_PICO_PATH environment variable set to the location of your pico-sdk (e.g. C:\VSARM\sdk\pico\pico-sdk) as we did in the previous tutorial.

Open C:\git-sdk-64\git-bash.exe (if it’s not already open).

Run the following commands to clone the picotool repository and build it:

cd /c/VSARM/sdk/pico

git clone -b master https://github.com/raspberrypi/picotool.git 

cd picotool

mkdir build

cd build

cmake -G "MinGW Makefiles" -DPC_LIBUSB_INCLUDEDIR="/c/git-sdk-64/mingw64/include/libusb-1.0" ..
make
Just like with OpenOCD, we’ll need a copy of libusb-1.0.dll to be in the same folder as picotool.exe. Enter the following command to copy it into the build folder:

cp /c/git-sdk-64/mingw64/bin/libusb-1.0.dll .

We also need a copy of libgcc_s_seh-1.dll:

cp /c/git-sdk-64/mingw64/bin/libgcc_s_seh-1.dll .

You can test picotool by entering the following:

./picotool.exe

It should spit out a help message and exit.

6:


Optional: You can add picotool.exe to your Path, if you feel so inclined. This will allow you to call “picotool” from any terminal.

Search for “environment variables” in the Windows search bar
Open “Edit the system environment variables”
Click “Environment Variables…”
Select Path under “User variables” and click “Edit…”
Add a new entry: C:\VSARM\sdk\pico\picotool\build (or whever you’re keeping picotool.exe)
Click OK on all the windows to save the settings
Note: At the time of writing, I could only get this build of picotool to run inside of Git Bash. I’m assuming that the MinGW environment is somehow necessary for its execution. If I find a way to make the .exe run in Command Prompt or PowerShell, I will post it here.

Update Driver
Windows does not have a native driver to send commands over USB to the Pico from picotool. So, we have to install one manually.

Put your Pico board into bootloader mode (press and hold BOOTSEL and plug in the USB cable). It should enumerate on your computer as a storage drive.

Head to https://zadig.akeo.ie and download Zadig.

Run Zadig. Select Options > List All Devices.

You should see 2 Pico devices in the drop-down menu: RP2 Boot (Interface 0) and RP2 Boot (Interface 1). Select RP2 Boot (Interface 1).

The driver should be listed as (NONE). Select libusb-win32 (some version) as the replacement driver.

7:

Click Install Driver. When it’s done, the current driver should be listed as “libusb-win32.”

If you break something in Zadig (like I did), you can fix it. For example, the Pico will not enumerate as a mass storage device drive in bootloader mode. Zadig will show something other than “USBSTOR” as the driver for RP2 (Interface 0). Here is how to delete the bad drivers and let Windows fix the mess you made:

Put Pico into bootloader mode
Open Device Manager
Click View > Devices by container
Expand RP2 Boot
For all entries under RP2 Boot:
Right-click on entry
Select Uninstall Device
Check “Delete the driver software for this device” (if asked)
Click Uninstall
Unplug Pico
Hold BOOTSEL and plug it back in to put it into bootloader mode again
Windows should automatically reinstall all the correct drivers
Test Picotool
With your Pico in bootloader mode, enter the following command:

/c/VSARM/sdk/pico/picotool/build/picotool.exe info

Note that you can run picotool without specifying the path name if you added its location to your Path.

If it is able to communicate with your connected Pico, you should see that printed.

8:

Going Further
Once you have finished building OpenOCD and picotool, you can uninstall Git for Windows SDK by simply deleting the C:\git-sdk-64 directory.

Hopefully, this has helped you get started with some advanced tools for programming and debugging on your Raspberry Pi Pico. If you would like to set up step-through debugging using VS Code, see this tutorial (link coming soon).

Happy hacking!



