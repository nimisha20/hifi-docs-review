---
title: Build High Fidelity
taxonomy:
  category: docs
---

This guide details instructions for building High Fidelity on your system. If you're looking for build guides specific to the OS of your preference, check out:

- [Windows 64-bit](./windows-64-bit)
- [OS X](./os-x)
- [Linux](./linux)
- [Android](./android)

**On This Page:**
+ [Dependencies](#dependencies)
+ [CMake External Project Dependencies](cmake-external-project-dependencies)
  + [CMake](#cmake)
  + [Qt](#qt)
  + [Vcpkg](#vcpkg)
  + [Generating Build Files](#generating-build-files)
  + [Variables](#variables)
  + [Finding Dependencies](#finding-dependencies)
+ [Optional Components](#optional-components)
  + [Build Options](#build-options)
  + [Developer Build Options](#developer-build-options)
  + [Devices](#devices)

## Dependencies

| Dependencies                                 | Version       |
| -------------------------------------------- | ------------- |
| [cmake](https://cmake.org/download/)         | 3.9           |
| [Qt](https://www.qt.io/download-open-source) | 5.10.1        |
| [Python](https://www.python.org/downloads)   | 3.6 or higher |



## CMake External Project Dependencies

These dependencies do not need to be installed manually. They are automatically downloaded on the platforms where they are required. 

| Dependencies                                                 | Version                  |
| ------------------------------------------------------------ | ------------------------ |
| [Bullet Physics Engine](https://github.com/bulletphysics/bullet3/releases) | 2.83                     |
| [glm](https://glm.g-truc.net/0.9.8/index.html)               | 0.9.8                    |
| [Oculus SDK](https://developer.oculus.com/downloads/)        | 1.11 (Win32) / 0.5 (Mac) |
| [OpenVR](https://github.com/ValveSoftware/openvr)            | 1.0.6 (Win32 only)       |
| [Polyvox](http://www.volumesoffun.com/)                      | 0.2.1                    |
| [QuaZip](https://sourceforge.net/projects/quazip/files/quazip/) | 0.7.3                    |
| [SDL2](https://www.libsdl.org/download-2.0.php)              | 2.0.3                    |
| [Intel Threading Building Blocks](https://www.threadingbuildingblocks.org/) | 4.3                      |
| [vcpkg](https://github.com/highfidelity/vcpkg)               |                          |
| [VHACD](https://github.com/virneo/v-hacd)                    |                          |
| [zlib](http://www.zlib.net/)                                 | 1.28 (Win32 only)        |
| [nvtt](https://github.com/highfidelity/nvidia-texture-tools) | 2.1.1 (customized)       |

The above dependencies will be downloaded, built, linked, and included by CMake where we require them. The CMakeLists files that handle grabbing each of the external dependencies can be found in the [cmake/externals folder](https://github.com/highfidelity/hifi/tree/master/cmake/externals). The resulting downloads, source files, and binaries will be placed in the `build/ext` folder in the subfolders for each external project.

These are not placed in your normal build tree when doing an out of source build, so they do not need to be re-downloaded and re-compiled every time the CMake build folder is cleared. If you want to force a re-download and re-compile of a specific external, you can simply remove that directory from the appropriate subfolder in `build/ext`. Should you want to force a re-download and re-compile of all externals, just remove the `build/ext` folder.

### CMake

Hifi uses CMake to generate build files and project files for your platform.

### Qt

In order for CMake to find the Qt5 find modules, you will need to set a `QT_CMAKE_PREFIX_PATH` environment variable pointing to your Qt installation.

This can either be entered directly into your shell session before you build or in your shell profile (e.g.: ~/.bash_profile, ~/.bashrc, ~/.zshrc - this depends on your shell and environment).

The path it needs to be set to will depend on where and how Qt5 was installed. For example:

```
export QT_CMAKE_PREFIX_PATH=/usr/local/Qt5.10.1/5.10.1/gcc_64/lib/cmake
export QT_CMAKE_PREFIX_PATH=/usr/local/qt/5.10.1/clang_64/lib/cmake/
export QT_CMAKE_PREFIX_PATH=/usr/local/Cellar/qt5/5.10.1/lib/cmake
export QT_CMAKE_PREFIX_PATH=/usr/local/opt/qt5/lib/cmake
```

### Vcpkg

Hifi uses vcpkg to download and build dependencies. You do not need to install vcpkg.

Building the dependencies can be lengthy and the resulting files will be stored in your OS temp directory. However, those files can potentially get cleaned up by the OS. In order to avoid this and having to redo the lengthy build step, you can set the following environment variable:

`export HIFI_VCPKG_BASE=/path/to/directory`

Where `/path/to/directory` is the path to a directory where you wish the build files to get stored.

### Generating Build Files

Create a build directory in the root of your checkout and run the CMake build from there. This will keep the rest of the directory clean.

```
mkdir build
cd build
cmake ..
```

If cmake gives you the same error message repeatedly after the build fails (e.g. you had a typo in the `QT_CMAKE_PREFIX_PATH` that you fixed but the `.cmake` files still cannot be found), try removing `CMakeCache.txt`.

### Variables

Any variables that need to be set for CMake to find dependencies can be set as ENV variables in your shell profile, or passed directly to CMake with a `-D` flag appended to the `cmake ..` command.

For example, to pass the `QT_CMAKE_PREFIX_PATH` variable during build file generation:

```
cmake .. -DQT_CMAKE_PREFIX_PATH=/usr/local/qt/5.6.1/lib/cmake
```

### Finding Dependencies

The following applies for dependencies we do not grab via CMake ExternalProject such as OpenSSL, or for dependencies you have opted not to grab as a CMake ExternalProject (via `-DUSE_LOCAL_$NAME=0`). The list of dependencies we grab by default as external projects can be found in [the CMake External Project Dependencies section](#cmake-external-project-dependencies).

You can point our [Cmake find modules](https://github.com/highfidelity/hifi/tree/master/cmake/modules) to the correct version of dependencies by setting one of the three following variables to the location of these dependencies.

In the examples below, the variable `$NAME` would be replaced by the name of the dependency in uppercase, and `$name` would be replaced by the name of the dependency in lowercase (ex: `OPENSSL_ROOT_DIR`, `openssl`).

- `$NAME_ROOT_DIR` - pass this variable to Cmake with the `-DNAME_ROOT_DIR= flag` when running Cmake to generate build files.
- `$NAME_ROOT_DIR` - set this variable in your `ENV`.
- `HIFI_LIB_DIR` - set this variable in your ENV to your High Fidelity lib folder, should contain a folder '$name'

## Optional Components

### Build Options

The following build options can be used when running CMake

- BUILD_CLIENT
- BUILD_SERVER
- BUILD_TESTS
- BUILD_TOOLS

### Developer Build Options

- USE_GLES
- DISABLE_UI

### Devices

You can support external input/output devices such as Leap Motion, MIDI, and more by adding each individual SDK in the visible building path. Refer to the readme file available in each device folder in [interface/external/](https://github.com/highfidelity/hifi/tree/master/interface/external) for the requirements for each device.

**See Also**

- [Windows 64-bit Build Guide](./windows-64-bit)
- [OS X Build Guide](./os-x)
- [Linux Build Guide](./linux)
- [Android Build Guide](./android)