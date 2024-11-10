
# _build
 A script to assist building software for many compiler/targets/options.  
 
 Copyright 2023-2024 Detlef Riekenberg  
 LICENSE: MIT


## How it works:
 * simple create a build directory with a given prefix,  
   followed by the expected compiler toolchain to use and  
   followed by additional compiler options for this build  

 * run this script to build your project for one or for multiple targets


### Directory name details
 * start the prefix with an underscore
 * use dots in a directory name to separate  
   the prefix, the toolchain and additional options  
   (naming example: _prefix.toolchain.option1.option2.option3)
 * the script ignores the leading underscore
 * all numbers in the prefix are stripped to get the build mode
 * the numbers in the prefix are used to add a 32bit / 64bit mode option
 * when the toolchain has one or more dash, the script extracts the triplet  
   from the toolchain name for builds with autotools and adds `--host=triplet` to configure
 * all additional compiler settings are separated with a dot


### Supported directory prefixes
 * _bin:       compile a file with command line options for all compiler
 * _bin32:     compile a file in 32bit mode (add -m32 to CFLAGS / CXXFLAGS)
 * _bin64:     compile a file in 64bit mode (add -m64 to CFLAGS / CXXFLAGS)

 * _build:     build with autotools with command line options for configure
 * _build32:   build with autotools in 32bit mode (add -m32 to CFLAGS / CXXFLAGS)
 * _build64:   build with autotools in 64bit mode (add -m64 to CFLAGS / CXXFLAGS)

 * _cmake:     build with cmake with command line options for the cmake tool
 * _cmake32:   build with cmake in 32bit mode (add -m32 to CFLAGS / CXXFLAGS)
 * _cmake64:   build with cmake in 64bit mode (add -m64 to CFLAGS / CXXFLAGS)

 * _make:       build an already configured project



# Usage
 `scriptname [--time] [args]`  

 `--time`     is optional and runs the commands with `time -p`  

 The name of this script is used to select the command  
 (hint: create links to this script with the other command names)  


### Available commands  
 * _bin:       compile a single source file  
               (args are required and added to the compile command)
 * _build:     build an autotools project (calls `configure` and `make`)  
               (optional args are added to `configure`)
 * _configure: call configure in all autotools directories (prefix: _build*)  
               (optional args are added to `configure`)
 * _make:      call `make` in all buildable directories (prefix: _build*, _cmake* and _make*)  
               (optional args are added to `make`)
 * _cmake:     [experimental] build a cmake project (calls `cmake` and `make`)  
               (optional args are added to `cmake`)
 * _link:      create symlinks for source files given on the command line  
               (args select the files to link)
 * _run:       execute the command. uses a starter, when needed  
               (args required: `command` to run and optional args)
 * _runstatic: execute the command. uses a static starter, when needed  
               (args required: `command` to run and optional args)


### Supported starters for "_run" and "_runstatic"
 * wine                windows program
 * dosemu2             dos program
 * node                javascript program (probably build with emscripten)
 * qemu-$arch          cross compiled linux program (used only with _run)
 * qemu-"$arch"-static cross compiled linux program (used only with _runstatic)

   * "arch" is the first part extracted from the triplet
   * a triplet is the same prefix as used for builds with autotools


### Additional environment variables
 * DEBUG     print debugging infos, when running the script  
 * C_OR_CXX  compiler selection (defautlt: BOTH)  
   * CC: use the toolchain as the C compiler  
   * CXX: use the toolchain as the C++ compiler  
   * when empty, use the toolchain as the C compiler and the C++ compiler  



# Examples
### Example for compiling a single source file
 * create a bunch of build directories:  
   _bin32.tcc  
   _bin64.tcc  
   _bin.gcc.-O0.-g.-DDEBUG  
   _bin.gcc.-Os  
   _bin.g++.-pthread  
   _bin.i686-w64-mingw32-gcc  
   _bin.clang  
   _bin.cosmocc  
   _bin.emcc.-sWASM=0  
   _bin.zig.cc.-target.x86_64-linux-musl  

 * compile a single sourcefile for all targets:  
   `_bin helloworld.c -o helloworld`  


### Example for building an autotools project
 * create a bunch of build directories:  
   _bin32.tcc  
   _bin64.tcc  
   _bin.gcc.-O0.-g.-DDEBUG  
   _bin.gcc.-Os  
   _bin.i686-w64-mingw32-gcc  
   _bin.g++.-pthread  
   _bin.clang  
   _bin.cosmocc  
   _bin.emcc.-sWASM=0  
   _bin.zig.cc.-target.x86_64-linux-musl  

 * build a project for all targets:  
   `_build`  


### Running all build results
 * run all build programs  
   `_run helloworld`  


### Additional created files/links during the build
 * lastbuild.h (utc seconds since epoche of last build) in the build directory
 * a link named .srcdir, where the source was detected


### Additional hints
When the project does not support building in a separate build-directory:  

 * Fix your project (or add a bug report for external projects) to enable out of tree builds  
 * As a workaround, you can create symlinks for needed source files and try to build with `_make`  
   Example: 
   `_link *.c *.h *.cc *.cpp *.hpp *.cxx *.hxx *.zig  Makefile`
   `_make`



## ToDo
 * dietlibc
 * zig as build system