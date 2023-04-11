# onscripter-insani
*A successor to the [onscripter-insani](http://nscripter.insani.org/) of old*

# Last Updated
11 April 2023

# Languages
- [English](#English)
- [Français](#Français)
- [Italiano](Italiano)

# English

## Introduction and History
onscripter-insani is a branch of ONScripter, which is an active project as of 2022 (https://onscripter.osdn.jp/onscripter.html).  ONScripter is a clean-room open-source implementation of NScripter -- a novel game creation engine that arguably helped to bootstrap the novel game boom in Japan.  Many companies used NScripter in order to create some of the classics of the genre, notable examples being みずいろ (*Mizuiro*) by Nekonekosoft and 月姫 (*Tsukihime*) by TYPE-MOON.

In 2005, a programmer named chendo contributed some patches back to ONScripter which, for the first time, allowed for one-byte characters to be used directly in ONScripter.  We at insani.org took on maintenance of a separate branch of ONScripter for the purposes of novel game localization.  This branch eventually became a full-on fork called ONScripter-EN (ons-en), which was maintained by Haeleth and Uncle Mion.  The last commits to this fork date back to 2011, and the project has not been actively maintained ever since.

As of February 2023, the binaries for ONScripter-EN had the following characteristics:

- They no longer function in modern macOS, likely related to security changes 
  brought about in Catalina and beyond
- Many sound files do not play at all
- They contained a serious bug in which you could not access the NScripter system menu (and therefore you were unable to save) when you reached a choice point

Furthermore, the source trees for ONScripter-EN and ONScripter are now very different -- which is to be expected when there is a 12 year gap between the two projects.  Over the course of these 12 years, ONScripter has ported in many of the enhancements that were in ONScripter-EN, and has had at least one complete reorganization of its files and filenames.  This means that direct  comparisons between the code of ONScripter-EN and ONScripter are now very difficult.

That being said, as of April 2023, the ONScripter-EN project is once again officially maintained, and indeed we are involved with helping to provide a build system on Windows (x86-64 and i686).  The project can be found at:

- https://github.com/Galladite27/ONScripter-en
- https://github.com/insani-org/onscripter-en-msys2-configure-makefile

This iteration of ONScripter-EN largely focuses on advanced functionality not currently in ONScripter, as well as legacy Win32 (one of the stated
targets for ONScripter-EN is 32-bit Windows XP virtual machines, for instance).  We have chosen to re-activate our onscripter-insani branch, as the 
goals of ONScripter-EN and our goals are slightly different.  We consider ONScripter-EN to be a sister project to onscripter-insani and we collaborate closely with the maintainers of said project (for instance, we maintain the Windows Makefiles for MSYS2-based compilation).  Our goals for onscripter-insani are as follows:

- Stay as close to upstream ONScripter as possible
- Write in functionality that truly allows legacy English mode (one-byte, 
  monospaced, using `) to work
- Provide builds for modern architectures, most notably for Apple Silicon
  macOS
- Focus on portability and portable compilation, rather than on replicating
  ONScripter-EN functionality
- Support the insani-localized novel game library for posterity

All changes made by onscripter-insani are wrapped in #if defined(INSANI) ... #endif blocks and this is done for the following reasons:

- If you compile without the INSANI flag, you'll get baseline ONScripter
- Since this is a branch, this allows us to rapidly adapt to any changes 
  Ogapee might make in the future

Should you wish to contribute code to onscripter-insani, we'd ask that you follow that convention.
  
## Required and Recommended Libraries/Utilities
- For macOS, our build system assumes that you are using [homebrew](https://brew.sh)
- For Windows, our build system assumes that you are using [MSYS2](https://msys2.org)

### Required Libraries
- libjpeg
- bzip2
- One of:
  - SDL-2.0 AND SDL1.2-compat
  - SDL-1.2
- SDL_image
- SDL_mixer
- SDL_ttf

### Recommended Libraries
- SMPEG
- libogg
- libvorbis
- libmad

### Required Utilities (macOS)
- dylibbundler
- GNU make

## Preparing Your Development and Build Environment

### GNU/Linux
Every Linux distribution will have its own package manager.  Simply make sure you get the required libraries using that package manager.  If you are on a decently modern Linux distribution version, then make sure that you go the SDL2 + SDL1.2-compat route, as opposed to the pure SDL1.2 route.  You will get breakage otherwise.

### macOS
After you have installed Homebrew (https://brew.sh), simply do:

```brew install jpeg jpeg-turbo bzip2 sdl2 sdl12-compat sdl_image sdl_mixer sdl_ttf smpeg libogg libvorbis make dylibbundler```

You will want to make sure that Homebrew's libraries directory:

- ```/opt/homebrew/lib``` on Apple Silicon
- ```/usr/local/``` on Intel

is added to your PATH variable.  It should be added by default after Homebrew is installed.

All binaries that you create through this methodology are specific to both the architecture (ARM64/x86-64) as well as the macOS version that you're compiling on.  We recommend maintaining virtual machines simply so you can build to certain targets:

- *Apple Silicon*: Ventura
- *Intel*: Catalina

We recommend these two respective macOS versions because they are major breakpoints that brought about significant security changes -- binaries from
prior versions of macOS often don't work in these versions, whereas binaries that do work in these versions will work in all subsequent versions to date.

### Windows

Install MSYS2 (https://www.msys2.org) and accept the default install location:

```C:\msys64```

After you have installed MSYS2, make sure that you are logging into the MINGW64 environment.  By default MSYS2 launches into the UCRT64 environment, so be careful.  Once you are in, update MSYS2:

```pacman -Syuu```

This will likely cause MSYS2 to close.  Relaunch into the MINGW64 environment and run that command again until nothing further happens.  Then run:

```pacman -S mingw-w64-x86_64-SDL mingw-w64-x86_64-SDL_ttf mingw-w64-x86_64-SDL_mixer mingw-w64-x86_64-SDL_image mingw-w64-x86_64-bzip2 mingw-w64-x86_64-libogg mingw-w64-x86_64-libvorbis mingw-w64-x86_64-freetype mingw-w64-x86_64-smpeg mingw-w64-x86_64-iconv mingw-w64-x86_64-zlib mingw-w64-x86_64-toolchain```

## Compilation Instructions

### GNU/Linux
```make -f Makefile.Linux.insani```

On Linux and gcc, you can fairly easily make a static build of your binary by passing in the -static option to gcc in the right place in the Makefile.

### macOS
```gmake -f Makefile.MacOSX.insani```

If you wish to make a game-specific app bundle, you can do so by running ./makedist.MacOSX.sh -- however, if you wish to actually distribute your 
builds, you are going to need an Apple developer account and you will have to fork over $99 a year.  Much more detailed instructions and notes can be found in the comments section of Makefile.MacOSX.insani.

### Windows
Within the MINGW64 environment of MSYS2:

```make -f Makefile.Win.insani```

The DLL dependencies for the resultant onscripter.exe are:

- libbrotlicommon.dll
- libbrotlidec.dll
- libbz2-1.dll
- libdeflate.dll
- libfreetype-6.dll
- libgcc_s_seh1.dll
- libglib-2.0.0.dll
- libgraphite2.dll
- libharfbuzz-0.dll
- libiconv-2.dll
- libintl-8.dll
- libjbig-0.dll
- libjpeg-8.dll
- libLerc.dll
- liblzma-5.dll
- libmad-0.dll
- libogg-0.dll
- libpcre2-8-0.dll
- libpng16-16.dll
- libSDL_image-1-2-0.dll
- libSDL_mixer-1-2-0.dll
- libsharpyuv0.dll
- libstdc++-6.dll
- libtiff-6.dll
- libvorbis-0.dll
- libvorbisfile-3.dll
- libwebp-7.dll
- libwinpthread-1.dll
- libzstd.dll
- SDL_ttf.dll
- SDL.dll
- zlib1.dll

This list is exhaustive and likely many of the DLLs are not needed for normal running.  All DLLs can be found at:

```C:\msys64\mingw64\bin\```

If you are distributing your build, you'll want to package up all of these DLLs alongside your onscripter.exe.

### All Other Platforms
We only provide builds for macOS and Windows, and we only do testing on Linux, macOS, and Windows; however, ONScripter itself has been successfully compiled for platforms as diverse as Android, iOS, Symbian, MacOS Classic, PSP, *BSD, and the list goes on.  However we have not modified the makefiles for these platforms, and we can't guarantee a successful compilation on any platform other than Linux, macOS, and Windows.  If you are compiling on any of those platforms, you will minimally want to make sure the -DINSANI define is present.  If you succeed on one of our non-supported platforms, please get in touch with us and contribute your modified Makefiles back.

## Why Dynamic Libraries?
Traditionally, there was a push to make sure that onscripter-insani and ONScripter-EN builds were built with static libraries, for ease of distribution among other things.  The problem is, bitrot sets in quite quickly -- and the presence of those static libraries can sometimes lead to crashes as time goes on and there are major changes in the underlying OS.

Our macOS build system depends on Homebrew, and you should never attempt to statically link Homebrew libraries as they tend to be truly OS version
dependent in a way that the dylibs are not.  Furthermore, for the purposes of macOS, you're canonically supposed to distribute the required dylibs inside the app bundle as our makedist.MacOSX.sh does.

For Windows, the MINGW64 DLLs are widely compatible with any modern version of x86-64 Windows, and in the case that they become incompatible, replacement with updated versions will be easy.

## Contacts
The original creator of ONScripter is Ogapee:

- https://onscripter.osdn.jp/onscripter.html

Please refrain from contacting Ogapee about anything you find in onscripter-insani, though, as this branch does have differences from mainline 
ONScripter.  Contact us through Github instead:

- http://nscripter.insani.org/
- https://github.com/insani-org/onscripter-insani/

## License
ONScripter-insani is distributed under the terms of the GNU Public  License (GPL) v2.  This license does not extend to the assets for any game run
in onscripter-insani -- those rights remain with the original creators.

A copy of the GPLv2 can be found under COPYING in this source directory.

## Changelog

### 20230410
- Now with 100% more insanity spirit
- Corrections to line-break system
- Corrections to behavior of @, \, and / in legacy english mode
- !s, !w, and #(color) now work in english mode
- Initial experimental support for UTF8 (0.utf)
- French and Italian documentation

### 20230308
- ONScripter upstream version: 20220816
- Added legacy_english_mode flag
- Set english_mode and legacy_english_mode to TRUE when a line that begins with ` is detected
- Created line-break system for legacy english mode
- Fixed a bug in which lines that begin with ` cannot be advanced from in any circumstance
- Minor tweaks to allow for usage of SDL2 + SDL12-compat
- macOS:
  - APPBUNDLE build flag which by default allows for saving of .sav and envdata files in ~/Documents/ONScripter/gamename instead of in the same  directory, and to allow for game asset detection within the app bundle itself
  - Modified makefile for macOS
  - Created macOS app bundle creation script
- Windows:
  - All-new makefile for Windows, based on MSYS2 rather than VC++
  - Edits to several files to allow proper MSYS2-based compilation
  - Backported in window icon feature (from 2005!)

## All Previous Versions
- http://nscripter.insani.org/changelog.html

# Français

# Italiano

