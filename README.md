[![Build Status](https://travis-ci.org/david942j/patchelf.rb.svg?branch=master)](https://travis-ci.org/david942j/patchelf.rb)
[![Dependabot Status](https://api.dependabot.com/badges/status?host=github&repo=david942j/patchelf.rb)](https://dependabot.com)
[![Code Climate](https://codeclimate.com/github/david942j/patchelf.rb/badges/gpa.svg)](https://codeclimate.com/github/david942j/patchelf.rb)
[![Issue Count](https://codeclimate.com/github/david942j/patchelf.rb/badges/issue_count.svg)](https://codeclimate.com/github/david942j/patchelf.rb)
[![Test Coverage](https://codeclimate.com/github/david942j/patchelf.rb/badges/coverage.svg)](https://codeclimate.com/github/david942j/patchelf.rb/coverage)
[![Inline docs](https://inch-ci.org/github/david942j/patchelf.rb.svg?branch=master)](https://inch-ci.org/github/david942j/patchelf.rb)
[![MIT License](https://img.shields.io/badge/license-MIT-blue.svg)](http://choosealicense.com/licenses/mit/)

# patchelf.rb

Implements features of NixOS/patchelf in pure Ruby.

## Installation

Available on RubyGems.org!
```
$ gem install patchelf
```

## Usage

```
$ patchelf.rb
# Usage: patchelf.rb <commands> FILENAME [OUTPUT_FILE]
#         --print-interpreter, --pi    Show interpreter's name.
#         --print-needed, --pn         Show needed libraries specified in DT_NEEDED.
#         --print-soname, --ps         Show soname specified in DT_SONAME.
#         --print-runpath, --pr        Show the path specified in DT_RUNPATH.
#         --set-interpreter, --interp INTERP
#                                      Set interpreter's name.
#         --set-soname, --so SONAME    Set name of a shared library.
#         --set-runpath, --runpath PATH
#                                      Set the path of runpath.
#         --force-rpath                According to the ld.so docs, DT_RPATH is obsolete,
#                                      patchelf.rb will always try to get/set DT_RUNPATH first.
#                                      Use this option to force every operations related to runpath (e.g. --runpath)
#                                      to consider 'DT_RPATH' instead of 'DT_RUNPATH'.
#         --version                    Show current gem's version.

```

### Display information
```
$ patchelf.rb --print-interpreter --print-needed /bin/ls
# interpreter: /lib64/ld-linux-x86-64.so.2
# needed: libselinux.so.1 libc.so.6

```

### Change the dynamic loader (interpreter)
```
# $ patchelf.rb --interp NEW_INTERP input.elf output.elf
$ patchelf.rb --interp /lib/AAAA.so /bin/ls ls.patch

$ file ls.patch
# ls.patch: ELF 64-bit LSB shared object, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib/AAAA.so, for GNU/Linux 3.2.0, BuildID[sha1]=9567f9a28e66f4d7ec4baf31cfbf68d0410f0ae6, stripped

```

### Change SONAME of a shared library
```
$ patchelf.rb --so libc.so.217 /lib/x86_64-linux-gnu/libc.so.6 ./libc.patch

$ readelf -d libc.patch | grep SONAME
#  0x000000000000000e (SONAME)             Library soname: [libc.so.217]

```

### Set RUNPATH of an executable
```
$ patchelf.rb --runpath . /bin/ls ls.patch

$ readelf -d ls.patch | grep RUNPATH
#  0x000000000000001d (RUNPATH)            Library runpath: [.]

```

### As Ruby library
```rb
require 'patchelf'

patcher = PatchELF::Patcher.new('/bin/ls')
patcher.get(:interpreter)
#=> "/lib64/ld-linux-x86-64.so.2"

patcher.interpreter = '/lib/AAAA.so.2'
patcher.get(:interpreter)
#=> "/lib/AAAA.so.2"

patcher.save('ls.patch')

# $ file ls.patch
# ls.patch: ELF 64-bit LSB shared object, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib/AAAA.so.2, for GNU/Linux 3.2.0, BuildID[sha1]=9567f9a28e66f4d7ec4baf31cfbf68d0410f0ae6, stripped

```

## Environment

patchelf.rb is implemented in pure Ruby, so it should work in all environments include Linux, maxOS, and Windows!
