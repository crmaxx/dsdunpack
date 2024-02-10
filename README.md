# DSD Unpack

This utility converts DSD audio between Sony DSF (.dsf) and Philips DSDIFF
(.dff) container formats, including decompression of DST-encoded DSDIFF files
and ID3 tags in both containers.

Parts of this project are based on the SACD Ripper project, particularly
libsacd and libdstdec. See https://sacd-ripper.github.io/ for more info.

Tested on Windows using MinGW 4.9.3 and MSVC 2013/2015. You will need
pthreads-win32 to build using MSVC.

# Install

clang
```
.\vcpkg install pthreads
git submodule update --init --recursive
```
