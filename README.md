# openj9-android-builds
Self-explained: these are working builds of OpenJ9 for Android (API 34+).

Patches last updated on:
- JDK mainline (26): 2025-07-15

# How to build
1. Prerequisites:
- A working build has only been reproduced with GCC 15.1, so GCC is recommended. To install GCC, type:
```
pkg install tur-repo
pkg install gcc-default-15
```
Because of this requirement, it may not be possible to compile OpenJ9 for Android outside of Termux. Clang is able to build OpenJ9, but the binaries are riddled with issues that I don't have the time and the skill to fix. Or you might have luck with NDK r17 which is the final version to feature GCC.
- libelf and libdwarf. libelf can be installed via `pkg`; libdwarf must be installed through compiling [sources](https://github.com/davea42/libdwarf-code). Note that libdwarf may install headers in locations that is not in scope of the build system. To fix it, symlink `include/libdwarf-2` to `include/libdwarf-0`.
- Normal dependencies when compiling Java: cups, X11 headers...
2. Compiling steps
- Clone the OpenJ9 JDK repository:
```
git clone git clone --depth 1 https://github.com/ibmruntimes/openj9-openjdk-jdk
```
- Apply the first patch, then run `get_source.sh`. You can get sources first, but the patch allows shallow cloning to save data.
- Enter the OpenJ9 repo, apply the second patch. Enter the OMR repo, apply the third patch.
- Run:
```
bash configure --with-native-debug-symbols=none --with-boot-jdk=$BOOT_JDK
```
Replace $BOOT_JDK with a valid path to a JDK N-1  or N-2 (only for early previews) instance. For example, if you want to compile JDK 26 EA, you must have at least a working JDK 24 instance. If you don't have that, you might have to compile OpenJDK 22 - 23 - 24. Or if you have glibc installed, use pre-built libraries from Adoptium...
- Now start the build:
```
make images
```
If you encounter errors about unimplemented functions, chances are the API level is defaulted to 24. Patch the `android/api-level.h` header to set the default API to 34. I might enable building on lower APIs, but probably not lower than 29 (Android 10).

Your build should complete, giving you a working OpenJ9 instance!
