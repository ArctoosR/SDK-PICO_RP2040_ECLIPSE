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


 5:
 
 my_pico_project/  
│── CMakeLists.txt      ← این فایل را اینجا قرار دهید  
│── pico-sdk/           ← مسیر Pico SDK  
│── src/                ← فایل‌های سورس پروژه  
│   ├── main.c          ← فایل اصلی برنامه  
│── build/              ← مسیر پیشنهادی برای خروجی‌های ساخت  
