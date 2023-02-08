## Template for RP2040 project with VScode/Freertos
----

VScode template 

### Template usage

* Copy directory "FreeRTOS_template" to a new directory.
* Rename directory
* Delete the following subdirectories if they exist
    * "build"
    * ".git"
* Edit following line of "CMakeLists.txt" 
    * "set (THE_PROJECT_NAME "Pico_FreeRTOS_template")  # set project name"
* Compile to check all is well (assumes a working Pico/VScode IDE system)
* Edit "READ.me" file to reflect new project
* Create LOCAL and GITHUB repositories through VScode 


### Tools

* Windows 10 laptop
* Visual Studio Code
* GCC 10.3.1 arm-none-eabi compiler
* CMake tools
* PicoProbe debug probe


Jim Herd February 2023
