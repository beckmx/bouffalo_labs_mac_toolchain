# GNU Toolchain on macOS and Apple Silicon

In this repository you will find all the necesary files to compile
the SDK from Bouffalo labs: https://github.com/bouffalolab/bouffalo_sdk/

## Installation
Basically you need to place this folder somewhere in your mac, in my machine I have this folder in this path:

`/opt/riscv-toolchain/`

Then, add the toolchain to the PATH environment like this:

`export PATH=$PATH:/opt/riscv-toolchain/bin`

## Patch the SDK
The toolchain is at least 80% of the work, but you also need to patch the SDK with some changes, the SDK remember must be clonned from this location https://github.com/bouffalolab/bouffalo_sdk/

### Patch project.build
Open the file "project.build" from the sdk and look for this code

        ifeq ($(OS),Windows_NT)
        CMAKE = $(BL_SDK_BASE)/tools/cmake/bin/cmake.exe
        BL_FLASH_PROGRAM = ${BL_SDK_BASE}/tools/bflb_tools/bouffalo_flash_cube/BLFlashCommand.exe
        cmake_generator = "MinGW Makefiles"
        else
    	ifeq ($(shell uname),Darwin)
    	CMAKE = cmake # use user cmake
    	BL_FLASH_PROGRAM = ${BL_SDK_BASE}/tools/bflb_tools/bouffalo_flash_cube/BLFlashCommand-macos
    	cmake_generator = "Unix Makefiles"
    	else
    	CMAKE = $(BL_SDK_BASE)/tools/cmake/bin/cmake
    	BL_FLASH_PROGRAM = ${BL_SDK_BASE}/tools/bflb_tools/bouffalo_flash_cube/BLFlashCommand-ubuntu
    	cmake_generator = "Unix Makefiles"
    	endif

    

	
Replace it with this:


    ifeq ($(OS),Windows_NT)
    CMAKE = $(BL_SDK_BASE)/tools/cmake/bin/cmake.exe
    cmake_generator = "MinGW Makefiles"
    else
    CMAKE = /opt/homebrew/bin/cmake
    BL_FLASH_PROGRAM = ${BL_SDK_BASE}/tools/bflb_tools/bouffalo_flash_cube/BLFlashCommand-macos
    cmake_generator = "Unix Makefiles"
    endif


### Patch bflb_flash.cmake

Find the bflb_flash.cmake file in the same SDK folder and replace this line
`set(TOOL_SUFFIX "-ubuntu")`
For this line (this is a lazy fix!):
`set(TOOL_SUFFIX "-macos")`


### Compile
Now, you can compile in the Apple M1 like this:
`make CHIP=bl616 BOARD=bl616dk`
`make flash CHIP=bl616 BOARD=bl616dk COMX=/dev/tty.xyz`

Enjoy!

