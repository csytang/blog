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
    Install LLVM on a Mac machine, please reference the tutorial here.
    Install LLVM on an Utunbu machine, please reference the tutorial here.
 2. Install python: Install python 3 on a Mac or an Utunbu, please download and install from the official site.

#### 3. Use Whole Program LLVM to build bc
 * Install WLLVM
 >bash pip install wllvm

 * Setup the system environment
 * Build the Linux
 >cd linux-stable make CC=wllvm defconfig make CC=wllvm -j64 2>&1 | tee build.log

 With the above step, an exectuable `vmlinux` is generated
 * Extract the bitcode
 >extract-bc vmlinux

