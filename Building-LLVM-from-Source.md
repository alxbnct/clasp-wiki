* Alternatively you might get llvm13 from your package manager, e.g. `brew` on macos
* `git clone https://github.com/llvm/llvm-project.git`
* `cd llvm-project`
* `git checkout tags/llvmorg-13.0.0`
* `mkdir -p build`
* `cd build`
* after each line, there should be a  ´\´
* edit the `LLVM_BINUTILS_INCDIR`correctly, or you get a `fatal error: 'plugin-api.h' file not found`
* for macosx it might be just `brew install binutils`. I didn't had ld.gold installed, so I did following https://llvm.org/docs/GoldPlugin.html
```c++
cmake -G "Unix Makefiles" \
        $xcode \
        -DLLVM_BINUTILS_INCDIR=/usr/local/opt/binutils/include \
        -DLLVM_ABI_BREAKING_CHECKS=FORCE_OFF \
        -DLINK_POLLY_INTO_TOOLS=ON \
        -DLLVM_BUILD_EXTERNAL_COMPILER_RT=ON \
        -DLLVM_BUILD_LLVM_DYLIB=ON \
        -DLLVM_ENABLE_ASSERTIONS=OFF \
        -DLLVM_ENABLE_EH=ON \
        -DLLVM_ENABLE_FFI=ON \
        -DLLVM_ENABLE_LIBCXX=ON \
        -DLLVM_ENABLE_RTTI=ON \
        -DLLVM_INCLUDE_DOCS=OFF \
        -DLLVM_INSTALL_UTILS=ON \
        -DLLVM_OPTIMIZED_TABLEGEN=ON \
        -DLLVM_TARGETS_TO_BUILD=X86 \
        -DLLVM_ENABLE_PROJECTS=clang\;libcxxabi\;libcxx\;lldb\; \
        -DCMAKE_BUILD_TYPE=Release \
        -DWITH_POLLY=ON \
        -DCMAKE_INSTALL_PREFIX=/opt/clasp \
        -DLLVM_CREATE_XCODE_TOOLCHAIN=ON \
        ../llvm
````
* my cmake tells me, the following, so perhaps these 2 variables need something else or are outdated:
```c++
CMake Warning:
  Manually-specified variables were not used by the project:

    LINK_POLLY_INTO_TOOLS
    WITH_POLLY
````
* `cmake --build .`
* `cmake --build . --target install`

* My complete wscript.config on Macos BigSur is the following
```c++
LLVM_CONFIG_BINARY = '/opt/clasp/bin/llvm-config'
USE_PARALLEL_BUILD = True
DEBUG_OPTIONS = [  "DEBUG_RELEASE"
                  ,"DEBUG_BCLASP_LISP"
                  ,"DEBUG_CCLASP_LISP"
                  ,"DEBUG_COMPILER"
                  ,"DEBUG_GUARD"
                  ,"DEBUG_VERIFY_MODULES"
# other useful options for debugging
                  ,"USE_HUMAN_READABLE_BITCODE"
                  ,"DEBUG_JIT_LOG_SYMBOLS"
                  ,"DEBUG_ASSERT_TYPE_CAST"
                  ]
REQUIRE_LIBFFI = True
INCLUDES = [ "/Library/Developer/CommandLineTools/usr/include/c++/v1/",
             "/Library/Developer/CommandLineTools/SDKs/MacOSX11.3.sdk/usr/include",
             "/opt/clasp-support/include"]
CPPFLAGS = [ "-Wno-nullability-completeness" ]
LINKFLAGS = ["-L/Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/usr/lib" , "-L/opt/clasp-support/lib"]
````
* in wscript I had to change
 
`XCODE_SDK = "/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX11.1.sdk"`

to

`XCODE_SDK "/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX11.3.sdk"`