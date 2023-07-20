First blog post, opening the scene with the simple [CMake](https://cmake.org/) hints.

During the development of my hobby C/C++ projects, I found myself spending too much time on dependency management. Especially during the initial steps of project.

Here is the basic overview for the initial dependency tasks I dealt with:
* Download the release version of dependency by respecting the compiler I'll use
* Configure the IDE that I'll be using for the dependency

Well, the list looks small but it starts to feel huge with every new project that I start. Repeating same tasks over and over again. Things needs to be automated, luckily CMake comes to rescue.

Let's first take a look at downloading and extracting operations with CMake.

### Downloading the dependencies with CMake
Let's assume we will be using [SDL](https://www.libsdl.org/) library for handling window creation stuff in our project and we will let CMake handle the integration part.

SDL provides it's releases on their [GitHub releases](https://github.com/libsdl-org/SDL/releases) page. We will utilize ``FetchContent_Declare`` and ``FetchContent_MakeAvailable`` commands to download release version of SDL and extract it on specific directory.

Basic example that downloads and extracts SDL for MSVC compiler looks like this:
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

First improvement to this script can be done by supporting other platforms and other compilers like GCC. Currently it is only supporting MSVC but SDL has releases for other compilers too.

Now let's take a look at much simpler task, setting up CMake variables for SDL.

### Setting up SDL related CMake variables
We will be using CMake module for letting CMake know about SDL's source files.

Make sure to update your CMake module path before using this script on build pipeline.

Example code for updating CMake module path looks like this:

``set( CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_SOURCE_DIR}/scripts/cmake/" )``

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

Now we set our CMake variables, it is time for linking them to project during build pipeline.

### Linking SDL during build process
We need specific CMake commands to use SDL in our source files.

The command below adds the header files of SDL as include directories. Pretty simple.
``include_directories( ${SDL2_INCLUDE_DIRS} )``

Now, to link SDL library files, we can use ``target_link_libraries`` command.
``target_link_libraries( qmack PRIVATE ${SDL2_LIBRARIES} )``

In terms of SDL integration, most of the repetitive tasks should be automated at this point.

### Wrapping up
These were the minimal script examples to automate some of the repetitive tasks that I faced during my development journey with C/C++ and SDL. As I mentioned before, scripts still can be improved by adding different logic for other platforms.

The full working example of this pipeline can be found in the 2D isometric engine that I am working on. You can check it out by visiting [Qmack Engine](https://github.com/iozsaygi/qmack-engine) repository.

Thank you for reading my very first blog post. Hopefully, I will post even more blog posts in the future!

