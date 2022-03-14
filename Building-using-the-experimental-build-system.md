This page documents the steps needed to build using the experimental build system being developed which uses a Lisp based configuration script that outputs [Ninja](https://ninja-build.org/) build files. This build system is not ready for general use. If you are trying build clasp or cando for general usage then use the waf based builder.

# Requirements

The build requirements of clasp must be installed before beginning the build. For each OS listed below install the packages as listed.

* Arch - `pacman -S binutils boost clang gc gmp libbsd libffi libunwind llvm ncurses ninja sbcl`
* Debian/Ubuntu - `apt install -y binutils-gold clang-13 libboost-dev libbsd-dev libclang-cpp13-dev libelf-dev libgc-dev libgmp-dev libncurses-dev libunwind-dev llvm-13 ninja-build sbcl zlib1g-dev`
* Fedora - `dnf install boost-devel clang elfutils-libelf-devel gc-devel gmp-devel libbsd-devel libffi-devel libunwind-devel llvm-devel ncurses-devel ninja-build sbcl zlib-devel`

# Building

Start with a clean Clasp clone on the `ninja` branch. If you have run the waf based build in your clone before then run `./waf distclean` and remove the `build` directory. Also remove any extension clones from the extensions directory. Then execute the following steps

1. Configure the system with `sbcl --script configure.lisp`
2. Build the system with `ninja -C build`

This build system does not install Clasp yet. If the build is successful then there will be a Clasp executable named `build/boehm/iclasp-boehm`.

# Extensions

The only extension that is currently compatible with this build system is [seqan-clasp](https://github.com/clasp-developers/seqan-clasp/). To enable this extension create a file `config.sexp` in the root of the clasp directory with the following contents then execute the build instructions listed in the previous section.

```lisp
(:extensions (:seqan-clasp))
```