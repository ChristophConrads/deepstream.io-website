---
title: Using the C++ Client Library
description: The entry point documentation for the deepstream.io C++ client
---

The C++ client library enables you to build deepstream.io clients. In this
tutorial, we will explain you
- which software the C++ clients depends on,
- how to compile and install the dependencies,
- how to compile and install the C++ client library, and
- we will show you how to write a simple client.

The deepstream.io C++ client library was tested
- on Raspbian, ARMv7, with GCC 4.9.2-10, Clang 3.5-10+rpi1, Flex
  2.5.39-8+deb8u2, Boost 1.55.0+dfsg-3, POCO 1.7.7, Valgrind 3.7.0-6+rpi2,
- on Amazon Linux AMI, x86-64, with GCC 4.8.3-3.20, Clang 3.6.2-1.12, Flex
  2.5.37-3.13, Boost 1.53.0-14.21, POCO 1.7.7, Valgrind 3.10.0-16.14,
- on OS X, x86-64, Apple LLVM 7.3.0 (clang-703.0.31), Flex 2.5.35
  Apple(flex-31), Boost 1.62.0, POCO 1.7.7, Valgrind 3.12.0.
It should work flawlessly on these systems.


## Requirements

For compiling the C++ client, you need
- a C99 compiler,
- a C++11 compiler,
- [Boost C++ libraries](http://www.boost.org) 1.46 or newer.
- [Flex](https://github.com/westes/flex/releases) 2.5 or newer, and
- [POCO C++ libraries](https://pocoproject.org) 1.7 or newer,


## Installing Dependencies on Linux

Clang and GCC available as stable releases for all distributions; with aptitude,
you can install the required third-party software by executing the following
commands in Bash:
```sh
sudo apt-get install gcc g++ libboost-dev libboost-test-dev flex
```
With the yum package manager, you have to issue
```sh
sudo yum install gcc gcc-c++ boost boost-devel flex
```
For most Linux distributions, no recent POCO release is available in the
repositories. Thus, here are build instructions: open a shell, and download POCO
with the following command:
```sh
wget https://pocoproject.org/releases/poco-1.7.7/poco-1.7.7.tar.gz
```
This command will download the file `poco-1.7.7.tar.gz` into the current folder.
Unpack the tarball:
```sh
tar -xf poco-1.7.7.tar.gz
```
There should now be a folder called `poco-1.7.7` containing the POCO C++
libraries source code. We can now build the library in a new folder called
`build` (this is called an *out-of-source build*) and have it installed in your
home directory:
```sh
mkdir build
cd build
cmake \
	-DCMAKE_INSTALL_PREFIX=~ \
	-DENABLE_CRYPTO=OFF \
	-DENABLE_DATA=OFF \
	-DENABLE_JSON=OFF \
	-DENABLE_MONGODB=OFF \
	-DENABLE_NETSSL=OFF \
	-DENABLE_PAGECOMPILER=OFF \
	-DENABLE_PAGECOMPILER_FILE2PAGE=OFF \
	-DENABLE_UTIL=OFF \
	-DENABLE_XML=OFF \
	-DENABLE_ZIP=OFF \
	-- ../poco-1.7.7
make
make install
```
The option `-DCMAKE_INSTALL_PREFIX` determines the target directory for the
installation; the tilde symbolizes your home directory. To remove the build
directory, execute
```bash
cd ..
rm -r build
```


## Installing Dependencies on OS X

By default, OS X ships with Clang and Flex installed. Moreover, all required
packages are available on Brew. Issue in the shell
```sh
brew install boost poco valgrind
```


## Installing the deepstream.io C++ Client Library

The deepstream C++ client sources can be found on [GitHub](https://github.com/deepstreamIO/deepstream.io-client-cpp). We will download and build the latest release. Execute the following statements in a Shell:
```bash
git clone 'git@github.com:deepstreamIO/deepstream.io-client-cpp.git'
mkdir build
cd build

cmake \
	-DCMAKE_INSTALL_PREFIX=~ \
	-DCMAKE_BUILD_TYPE=Release \
	-- ../deepstream.io-client-cpp
make
make install
```
If you have Doxygen installed on your computer, you can also generate
documentation by executing `make doc` in the CMake build directory.  Clean up
with the following commands:
```bash
cd ..
rm -r build
```


### Getting Started

In C++, include `<deepstream.hpp>` in your code to start implementing clients.
All code is located in the namespace `deepstream`, type names start with a
capital letter and use camelCase, function names are all lower case using pascal
case, and names of constants are capitalized with pascal case.

To log in on a server without authentification, compile and execute the
following code with a deepstream.io server running on port 6020.
```c
#include <cstdio>
#include <exception>
#include <deepstream.hpp>

int main()
try
{
	deepstream::Client client("ws://localhost:6020/deepstream");
	if( client.login() )
		std::printf( "Client logged in\n" );
	else
		std::printf( "Client not logged in\n" );
}
catch(std::exception& e)
{
	fprintf( stderr, "error: '%s'\n", e.what() );
	return 1;
}
```


## Data Formats

The deepstream C++ client works only with serialized data. This means, you have
to take care of serialization and deserialization yourself. The deepstream
protocol knows about several types of data; the type of serialized data can be
inferred from a one-letter prefix at the beginning, e.g., strings are encoded by
prepending the letter `S` such that the text `identifier` would be encoded as
`Sidentifier`. Below is the [list of data
types](https://deepstream.io/docs/common/constants/#data-types):
- UTF-8 strings: `S`,
- JSON objects: `O`,
- JavaScript numbers (floating-point numbers): `N`,
- NULL: `L`,
- the boolean value `true`: `T`,
- the boolean value `false`: `F`,
- an undefined value: `U`.
