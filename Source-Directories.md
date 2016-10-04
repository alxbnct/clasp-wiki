./bin
- Not useful

./build
- This is where the built version of clasp gets generated.

./build/clasp
- This contains the entire built clasp directory.  The idea is this entire subtree can be copied to /Applications on OS X or /usr/local/xxx on linux and everything is in there that is necessary to run clasp and expose C++ libraries to clasp.

./docs
- Doxygen files for setting up doxygen documentation for the clasp source code.

./examples
- Example code for doing things like exposing C++ libraries to clasp and using clasp to interrogate and refactor C++ code.

./examples/cxxast
- An example that uses the clang ASTMatcher library exposed within Clasp to interogate a C++ soruce file.

./externals
- boost build "jam" files for external libraries that clasp depends on.

./include
./include/clasp
- All clasp header files are under this directory. The idea is that this can be copied to /usr/include/clasp and everything necessary to interface C++ libraries to clasp will be available there.

./licenses
- Files that describe the various licenses that the parts of clasp are distributed within.

./src
- All clasp source code.

./src/asttooling
- The clang AST and ASTMatcher C++ API's are exposed within this directory.

./src/cando
- Not useful - should be removed - this used to contain the C++ molecular design code that clasp was designed to support for my research.

./src/cffi
- These are stubs for clasp's CFFI library.  It's not functional yet but will be fleshed out so that clasp can use FFI libraries just like every other Common Lisp.  It's much easier to use "clbind" to expose C++ and C libraries at the moment so I use those.

./src/clbind
- The "clbind" C++ template metaprogramming library built into clasp that is used to expose C++ libraries within clasp Common Lisp.  Clbind is used by clasp/src/asttooling to expose the clang AST and ASTMatcher libraries. It is not used to expose the core clasp C++ code because they use a similar but more intimate CL binding library that allows the garbage collector to directly manage Common Lisp classes implemented in C++.

./src/common
- A bunch of scripts used by the build system that scrape the clasp C++ source code for information on exposed classes and symbols.   registerClasses.py and symbolScraper.py are really important.  pump.py is "Google pump" - a program that generates C++ template metaprogramming code from a template.

./src/core
- This directory contains almost all of the clasp core C++ code. The original idea was that this code and src/gctools would be all that is necessary to start the clasp interpreter.  That may not be completely true at this point.

./src/gctools
- This directory contains the code that interfaces with the Boehm and MPS garbage collectors.  It also contains the code for smart_ptr<xxx>, weak pointers and allocators.

./src/include
- This directory is not useful.  I think it's been replaced by clasp/include/xxx

./src/lisp
- This directory contains all of the Common Lisp source code that clasp needs.

./src/llvmo
- The interface to the LLVM C++ API is exposed in the source code in this directory. It uses the more intimate "clbind" library to interface to the code in src/core.

./src/main
- This contains main.cc - the source file that contains "main" and that starts everything up.
It also contains gc_interface.cc and clasp_gc.cc.  These two files interface with the MPS garbage collector.  clasp_gc.cc is generated by a static analyzer written in Common Lisp that uses the tools in clasp/src/asttooling to analyze the clasp C++ source code.

./src/mpip
- The MPI (Message Passing Interface) library is exposed here.  Clasp is designed to run on large supercomputers and use the MPI library to communicate between processes distributed across hundreds of thousands of CPU nodes.

./src/mps
- This is a fork of the Memory Pool System (MPS) garbage collector by Ravenbrook Inc.
It was slightly hacked by David Lovemore (who worked at Ravenbrook at the time) to support Clasp. I think we ended up disabling incremental GC because it was causing problems with clasp.  This may be fixed now that clasp has evolved.

./src/serveEvent
- I think this is a port of the SBCL serve-event library.  I wrote this to support SLIME. I don't think it's being used by SLIME at the moment.

./src/sockets
- A port of the SBCL socket library. This is necessary to support SLIME.

./src/tests
- A whole bunch of source files I've written over the years to test aspects of clasp.  It's a bit of a dogs breakfast I'm afraid.

./targets
-This hierarchy contains files to support different operating systems. Currently only a few files in clasp/targets are used.  Only OSX and Linux are currently supported.