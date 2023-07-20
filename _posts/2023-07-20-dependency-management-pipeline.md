First blog post, opening the scene with the simple [CMake](https://cmake.org/) hints.

During the development of my hobby C/C++ projects, I found myself spending too much time on dependency management. Especially during the initial steps of project.

Here is the basic overview for the initial dependency tasks I dealt with:
* Download the release version of dependency by respecting the compiler I'll use
* Configure the IDE that I'll be using for the dependency

Well, the list looks small but it starts to feel huge with every new project that I start. Repeating same tasks over and over again. Things needs to be automated, luckily CMake comes to rescue.

### Downloading the dependency with CMake
Let's assume we will be using [SDL](https://www.libsdl.org/) library for handling window creation stuff in our project.

Luckily SDL provides it's releases on their [GitHub releases](https://github.com/libsdl-org/SDL/releases) page. We will be using ``FetchContent_Declare`` and ``FetchContent_MakeAvailable`` commands to download release version of SDL and extract it on specific directory.

Basic example that downloads and extracts SDL for MSVC compiler:
```cmake
if ( WIN32 )
    set( SDL2_DOWNLOAD_URL "https://github.com/libsdl-org/SDL/releases/download/release-2.28.1/SDL2-devel-2.28.1-VC.zip" )
    set( SDL2_DOWNLOAD_PATH "${CMAKE_CURRENT_LIST_DIR}/dependencies" )

    include( FetchContent )

    message( STATUS "Downloading dependencies." )
    FetchContent_Declare( SDL2 URL ${SDL2_DOWNLOAD_URL} SOURCE_DIR ${SDL2_DOWNLOAD_PATH}/SDL2 )
    FetchContent_MakeAvailable( SDL2 )
else()
    message( FATAL_ERROR "Can not setup download path for dependencies in current platform." )
endif()
```

The basic improvement to this script can be done by supporting other platforms and other compilers like GCC.

With this simple chain of CMake commands, we are able to download and extract SDL library during build process. Which saves us from one of the repetitive tasks!