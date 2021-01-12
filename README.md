# CMake Target Resources

This utility add a target_resources function to cmake.
There are many ugly solutions out.
Therefor I wanted to show a better way.
This utility uses as idomatic code as possible:
- no global variable
- cmake like syntax

The only downside of this utility is, that it creates an extra target for every target that has resources. (But only for these)
This is because it is not possible to create dependencies between targets and custom command (which are used to copy the resources) after the target was created.
It is a design decision that this downside is accepted in order to have a clean and idomatic interface.

When the utility is set up you can use the target_resources function much like the target_sources function from cmake itself.
However the visipility parameter (PRIVATE/PUBLIC/INTERFACE) is not required.

CMakeLists.txt:
```cmake
cmake_minimum_required(VERSION 3.18)

# define our target name here.
# if needed we can change the
# name here instead all over
# the file.
set(THIS MyTargetName)

# just a sample target.
# every type of target
# could be used here.
add_library(${THIS} STATIC)

# to demonstrate the idomatic
# cmake interface
target_sources(${THIS} PUBLIC
        inc/asset/Asset.hpp
        inc/asset/Utils.hpp)

# and here the utility interface
target_resources(${THIS}
        res/images/Cat_02.png
        res/images/Dog_01.jpg
        config.ini)
```

This utility also features a function to copy resource folders or compressed archives.
At the moment only .tar.gz archives are supported.

```cmake
# copies all files inside a directory
# into the binary directory
target_resource_directories(${THIS} res/glsl)

# extracts all files from an archive
# into the binary directory.
# this is usefull if you want to
# access raw data by the binary output
# but dont want to have uncompressed
# raw data in you repository.
target_tar_gz_resources(${THIS}
        res/materials/BlackGranite.tar.gz
        res/materials/HammeredCopper.tar.gz)
```

## Offline Setup

There are multiple ways you can do this.
1. simply download the file once and place it into your project
2. create a git submodule

If you always want to have the newest version of this utility, the git submodule or the [online version](#Online Setup) is prefered.

## Online Setup

To use always the latest version of this utility, it is possible to just download it.

Here is a template for doing exactly this.
Notice that you will need an internet connection on the first cmake run!

cmake/target_resources.cmake:
```cmake
cmake_minimum_required(VERSION 3.18)

if (NOT EXISTS ${CMAKE_BINARY_DIR}/target_resources.cmake)

    file(DOWNLOAD
         https://raw.githubusercontent.com/rawbby/CMakeTargetResources/master/target_resources.cmake
         ${CMAKE_BINARY_DIR}/target_resources.cmake
         TLS_VERIFY ON)

endif ()

include(${CMAKE_BINARY_DIR}/target_resources.cmake)
```
