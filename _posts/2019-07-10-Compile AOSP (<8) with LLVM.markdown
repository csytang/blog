---
layout: post
title:  "Compile AOSP(<8) with LLVM"
date:   2019-07-10 11:09:00 +0700
categories: [llvm]
---
本文参考了

>https://dimjasevic.net/marko/2015/11/09/getting-llvm-bitcode-with-clang-from-android-take-ii/index.html

1. Download the Whole Program LLVM tool (`wllvm`) from 
   [https://github.com/travitch/whole-program-llvm](https://github.com/travitch/whole-program-llvm)

2. Add `wllvm`’s directory to the PATH_variable:

3. Set the compiler to be used by `wllvm` to Clang:
    >export LLVM_COMPILER=clang

4. For `wllvm` set a path to LLVM tools to the local prebuilt version distributed with AOSP:

    ```
     export LLVM_COMPILER_PATH=/path/to/android-repo/
        prebuilts/clang/host/linux-x86/3.8/bin
    ```

5. The trick is in not following instructions from the wllvm website, but dirty-hacking AOSP’s build system. In particular, modify its build/core/clang/config.mk such that CLANG and CLANG_CXX have the following values:
    ```
    CLANG:=/path/to/wllvm
    CLANG_CXX:=/path/to/wllvm++
    ```

6. Set `LOCAL_CLANG:=true` in `build/core/clear_vars.mk`.

7. Run the build process (Replace aosp_x86-eng with your target). The wllvm tool will likely give bunch of warnings on unknown parameters, but it’s safe to ignore the warnings:

```
    source build/envsetup.sh
    lunch aosp_x86-eng
    make -j32 Bluetooth
```

8. Link all of the bitcode into a single whole-library bitcode file. The extract-bc tool is part of the Whole Program LLVM project:

```
    extract-bc out/target/product/generic_x86/
      obj/lib/libbluetooth_jni.so
```






