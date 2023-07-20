First blog post, opening the scene with the simple [CMake](https://cmake.org/) hints.

During the development of my hobby C/C++ projects, I found myself spending too much time on dependency management. Especially during the initial steps of project.

Here is the basic overview for the initial dependency tasks I dealt with:
* Download the release version of dependency by respecting the compiler I'll use
* Configure the IDE that I'll be using for the dependency

Well, the list looks small but it starts to feel huge with every new project that I start. Repeating same tasks over and over again. Things needs to be automated, luckily CMake comes to rescue.

### Downloading the dependency with CMake
Let's assume we will be using [SDL](https://www.libsdl.org/) library for handling window creation stuff in our project.

Luckily SDL provides it's releases on their [GitHub releases](https://github.com/libsdl-org/SDL/releases) page. We will utilize ``FetchContent_Declare`` and ``FetchContent_MakeAvailable`` commands to download release version of SDL and extract it on specific directory.

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

### Informing CMake about SDL
Next, we need a script to set some CMake variables that are related to SDL.

The following script sets the listed variables below:
- ``SDL2_INCLUDE_DIRS`` is set to path of the SDL's header files
- ``SDL2_LIBRARIES`` is set to path of the SDL's library files

The script also provides architectural checks to disable usage of ``x86`` bit SDL library.

```cmake
set( SDL2_INCLUDE_DIRS "${CMAKE_SOURCE_DIR}/qmack/dependencies/SDL2/include/" )

# Check if we are looking for x64 or x86 builds.
if ( ${CMAKE_SIZEOF_VOID_P} MATCHES 8 )
  set( SDL2_LIBRARIES "${CMAKE_SOURCE_DIR}/qmack/dependencies/SDL2/lib/x64/SDL2.lib;${CMAKE_SOURCE_DIR}/qmack/dependencies/SDL2/lib/x64/SDL2main.lib" )
else()
  message( FATAL_ERROR "Only x64 builds are allowed." )
endif()

string( STRIP "${SDL2_LIBRARIES}" SDL2_LIBRARIES )
```

### Linking SDL source files
After setting CMake variables, we need to include source files of SDL library to our project.

The command below links the header files for SDL to our build configuration.
``include_directories( ${SDL2_INCLUDE_DIRS} )``

Now, to link SDL library files, we can use ``link libraries`` command.

``target_link_libraries( qmack PRIVATE ${SDL2_LIBRARIES} )``

### Finishing words
These were the minimal scripts for automating some of the essential dependency tasks in build pipeline. The full working version of this pipeline can be found on the 2D isometric engine that I am working on, the [Qmack Engine](https://github.com/iozsaygi/qmack-engine).

Thank you for reading my very first blog post, hopefully the more posts will come in the future.

