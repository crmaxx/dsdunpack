# DSD Tools

This utility converts DSD audio between Sony DSF (.dsf) and Philips DSDIFF
(.dff) container formats, including decompression of DST-encoded DSDIFF files
and ID3 tags in both containers.

Parts of this project are based on the DSD Unpack project. 
See https://github.com/michaelburton/dsdunpack for more info.

Tested on Windows using clang from MSVC 2022. You will need
PThreads4W from vcpkg to build using MSVC.

# Install

clang
```
.\vcpkg install pthreads
git submodule update --init --recursive
```

# TODO
- Cue split
- Work with ID3 tags (write from cue, manual override)
