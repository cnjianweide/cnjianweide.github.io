---
layout: post
title: "How to install OpenSMILE 2.3.0 on Ubuntu"
comments: true
description: "A brief introduction with troubleshooting"
keywords: "OpenSMILE, Ubuntu, troubleshooting"
---
# 1. Unpacking
tar -zxvf opensmile-2.3.0.tar.gz 

# 2. Install autotools, libtool, make, gcc and g++
sudo apt-get install libtool
sudo apt-get install autotools-dev
sudo apt-get install autoconf
and so on...

# 3. execute buildStandalone
sudo bash buildStandalone.sh

## 3a Troubleshooting with build problem
Ecoutered problem "error: narrowing conversion of ‘'\37777777756'’ from ‘char’ to ‘unsigned char’ inside { } [-Wnarrowing]
unsigned char smileMagic[] = {(char)0xEE, (char)0x11, (char)0x11, (char)0x00};"
Source: https://github.com/naxingyu/opensmile/issues/2
Solution:
- Newer gcc Version does not tolerate cast from char to unsigned char, thus you need to change the casts of the consts from char to unsigned char in vectorTransform.hpp in line 117. It will be solved in version 2.4. which will be released by audEERING soon.
- I also encountered with that issue.
If you are too 'lazy' to change it manually, you can type the following command on the terminal when you are in opensmile-2.3.0:
sed -i '117s/(char)/(unsigned char)/g' src/include/core/vectorTransform.hpp

If you are on the same level with opensmile-2.3.0 directory, you can type:
sed -i '117s/(char)/(unsigned char)/g' opensmile-2.3.0/src/include/core/vectorTransform.hpp

## 3b Build again
execute the command again and I saw:

```bash
----------------------------------------------------------------------
Libraries have been installed in:
/home/jianwei/opt/opensmile-2.3.0/inst/lib

If you ever happen to want to link against installed libraries
in a given directory, LIBDIR, you must either use libtool, and
specify the full pathname of the library, or use the '-LLIBDIR'
flag during linking and do at least one of the following:
- add LIBDIR to the 'LD_LIBRARY_PATH' environment variable
during execution
- add LIBDIR to the 'LD_RUN_PATH' environment variable
during linking
- use the '-Wl,-rpath -Wl,LIBDIR' linker flag
- have your system administrator add LIBDIR to '/etc/ld.so.conf'

See any operating system documentation about shared libraries for
more information, such as the ld(1) and ld.so(8) manual pages.
----------------------------------------------------------------------
/bin/mkdir -p '/home/jianwei/opt/opensmile-2.3.0/inst/bin'
/bin/bash ./libtool --mode=install /usr/bin/install -c SMILExtract '/home/jianwei/opt/opensmile-2.3.0/inst/bin'
libtool: install: /usr/bin/install -c SMILExtract /home/jianwei/opt/opensmile-2.3.0/inst/bin/SMILExtract
make[1]: Nothing to be done for 'install-data-am'.
make[1]: Leaving directory '/home/jianwei/opt/opensmile-2.3.0'

build successfull. You can now use the inst/bin/SMILExtract binary.
```

We can now move on to test it.

# 4. Test
./SMILExtract -C config/demo/demo1_energy.conf -I example-audio/opensmile.wav -O opensmile.energy.csv

## Result
Some text from opensmile.energy.csv
```bash
frameIndex;frameTime;pcm_LOGenergy
0;0.000000;-1.383729e+01
1;0.010000;-1.344107e+01
2;0.020000;-1.362546e+01
3;0.030000;-1.371792e+01
```
