---
layout: post
title:  "Build an LLVM Bitcode for Linux"
date:   2019-06-09 10:18:00 +0700
categories: [llvm]
---

This post shows how to build an LLVM bitcode (namely .bc image ) for Linux;

#### 1. Introduction

This repository presents how to build an LLVM bitcode. Specifically, here, we focus on Linux.

 * LLVM/Clang version 7.0.1
 * Python 3+ 

#### 2. Download Linux Source Code

Here, we demo how to download the v4.12-r5 linux source code with git

```console
git clone git://git.kernel.org/pub/scm/linux/kernel/git/stable/linux-stable.git
cd linux-stable
git reset --hard v4.12-rc5
```

#### 3. Install LLVM, Python

The next step is to install LLVM, python;
 1. Install LLVM:
    * Install LLVM on a Mac machine, please reference the tutorial [here](https://embeddedartistry.com/blog/2017/2/20/installing-clangllvm-on-osx).
    Install LLVM on an Utunbu machine, please reference the tutorial [here](https://linuxhint.com/install-llvm-ubuntu/).
 2. Install python: Install python 3 on a Mac or an Utunbu, please download and install from the [official site](https://www.python.org).

#### 3. Use Whole Program LLVM to build bc
 * Install WLLVM
 >bash pip install wllvm

 * Setup the system environment
    WLLVM includes four python executables: `wllvm` for compiling C code and `wllvm++` for compiling C++, an auxiliary tool `extract-bc` for extracting the bitcode from a build product (object file, executable, library or archive), and a sanity checker, `wllvm-sanity-checker` for detecting configuration oversights.
   
   Three environment variables must be set to use these wrappers:
   
    * `LLVM_COMPILER` should be set to either `dragonegg` or `clang`.
    * `LLVM_GCC_PREFIX` should be set to the prefix for the version of gcc that should
      be used with dragonegg.  This can be empty if there is no prefix.  This variable is
      not used if `$LLVM_COMPILER == clang`.
    * `LLVM_DRAGONEGG_PLUGIN` should be the full path to the dragonegg plugin.  This
      variable is not used if `$LLVM_COMPILER == clang`.
   
   Once the environment is set up, just use `wllvm` and `wllvm++` as your C
   and C++ compilers, respectively.
   
   
   In addition to the above environment variables the following can be optionally used:
   
    * `LLVM_CC_NAME` can be set if your clang compiler is not called `clang` but
       something like `clang-3.7`. Similarly `LLVM_CXX_NAME` can be used to describe
       what the C++ compiler is called. Note that in these sorts of cases, the environment
       variable `LLVM_COMPILER` should still be set to `clang` not `clang-3.7` etc.
       We also pay attention to the environment variables `LLVM_LINK_NAME` and `LLVM_AR_NAME` in an
       analagous way,  since they too get adorned with suffixes in various Linux distributions.
    * `LLVM_COMPILER_PATH` can be set to the absolute path to the folder that
      contains the compiler and other LLVM tools such as `llvm-link` to be used.
      This prevents searching for the compiler in your PATH environment variable.
      This can be useful if you have different versions of clang on your system
      and you want to easily switch compilers without tinkering with your PATH
      variable.
      Example `LLVM_COMPILER_PATH=/home/user/llvm_and_clang/Debug+Asserts/bin`.
   * `WLLVM_CONFIGURE_ONLY` can be set to anything. If it is set, `wllvm`
      and `wllvm++` behave like a normal C or C++ compiler. They do not
      produce bitcode.  Setting `WLLVM_CONFIGURE_ONLY` may prevent
      configuration errors caused by the unexpected production of hidden
      bitcode files. It is sometimes required when configuring a build.

 * Build the Linux
   1. cd linux-stable 
   2. make CC=wllvm defconfig 
   3. make CC=wllvm -j64 2>&1 \| tee build.log

 With the above step, an exectuable `vmlinux` is generated
 * Extract the bitcode
 >extract-bc vmlinux

