in all cases checkout the newest sources from clasp in the dev brach
# MacOSX Mojave
* brew install llvm@9
* adapt wscript.config
  * change LLVM_CONFIG_BINARY to the correct path, in my case
`LLVM_CONFIG_BINARY = '/usr/local/opt/llvm@9/bin/llvm-config'`
  * add the following two settings (this assumes, you have the command line tools installed)
```c++
CPPFLAGS = [ "-Wno-nullability-completeness"]
INCLUDES = [ "-I", "/Library/Developer/CommandLineTools/usr/include/c++/v1",
             "-I", "/Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/usr/include"
]
````
  * My complete wscript.config is the following
```c++
#LLVM_CONFIG_BINARY = '/usr/local/opt/llvm@6/bin/llvm-config'
LLVM_CONFIG_BINARY = '/usr/local/opt/llvm@9/bin/llvm-config'
USE_PARALLEL_BUILD = True
USE_BUILD_FORK_REDIRECT_OUTPUT = False
CLASP_BUILD_MODE = 'object'
DEBUG_OPTIONS = [  "DEBUG_RELEASE"
                  ,"DEBUG_BCLASP_LISP"
                  ,"DEBUG_CCLASP_LISP"
                  ,"DEBUG_COMPILER"
                  ,"DEBUG_GUARD"
                  ,"DEBUG_GUARD_VALIDATE"
#                 ,"DEBUG_JIT_LOG_SYMBOLS"
#                 ,"DEBUG_SLOW"
#                 ,"DEBUG_DTRACE_LOCK_PROBE"
                  ,"DEBUG_ASSERT_TYPE_CAST"
#                 ,"USE_HUMAN_READABLE_BITCODE"
                  ,"CST"
#                 ,"DISABLE_CST"
#	              ,"DEBUG_LLVM_OPTIMIZATION_LEVEL_0"
                  ]
REQUIRE_LIBFFI = True
CPPFLAGS = [ "-Wno-nullability-completeness"]
INCLUDES = [ "-I", "/Library/Developer/CommandLineTools/usr/include/c++/v1",
             "-I", "/Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/usr/include"
]
````
# Linux
In Linux I installed from scratch on top of Ubuntu 18.04 LTS
* (I needed sudo) `apt install -y gcc g++ llvm clang-6.0 libclang-6.0-dev cmake libgc-dev libgmp-dev binutils-gold binutils-dev zlib1g-dev libncurses-dev libboost-filesystem-dev libboost-regex-dev libboost-date-time-dev libboost-program-options-dev libboost-system-dev libboost-iostreams-dev libunwind-dev liblzma-dev libelf1 libelf-dev libbsd-dev sbcl`
* sudo apt install git
Now you need to update to llvm@9
* Taken from http://apt.llvm.org/ Install (stable branch)
  * LLVM
`sudo apt-get install libllvm-9-ocaml-dev libllvm9 llvm-9 llvm-9-dev llvm-9-doc llvm-9-examples llvm-9-runtime`
  * Clang and co
`sudo apt-get install clang-9 clang-tools-9 clang-9-doc libclang-common-9-dev libclang-9-dev libclang1-9 clang-format-9 python-clang-9 clangd-9`
  * libfuzzer
`sudo apt-get install libfuzzer-9-dev`
  * lldb
`sudo apt-get install lldb-9`
  * lld (linker)
`sudo apt-get install lld-9`
  * libc++
`apt-get install libc++-9-dev libc++abi-9-dev`
  * OpenMP
`sudo apt-get install libomp-9-dev`
* `git clone https://github.com/clasp-developers/clasp.git`
* `git checkout dev`
* `cp wscript.config.debian10 wscript.config`
* edit in wscript.config setting LLVM_CONFIG_BINARY = '/path/to/llvm-config-4.0' to the corrrect path, in my case /usr/bin/llvm-config-9
* `chmod +x wscript`
* `./waf distclean configure build_cboehm` (can take 1-2 hours)
* Start with `build/clasp`
# Known errors
* Disassemble no longer works
* cl:format no longer works correctly (pr being done)
* Backtrace in slime or with (core:btcl) or with (core:safe-backtrace) are slightly broken