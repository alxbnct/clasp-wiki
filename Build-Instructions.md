# Clasp latest

If something fails while building the `dev` branch, then try the `testing` branch. It's lagging behind somewhat, but it should be more stable.

You may want to look at [the Dockerfiles](https://github.com/clasp-developers/clasp/blob/dev/tools/dockerfiles/). They describe how to build in various environments and configurations.

## Linux

1. Debian/Ubuntu: `apt install -y gcc g++ llvm clang-6.0 libclang-6.0-dev cmake libgc-dev libgmp-dev binutils-gold binutils-dev zlib1g-dev libncurses-dev libboost-filesystem-dev libboost-regex-dev libboost-date-time-dev libboost-program-options-dev libboost-system-dev libboost-iostreams-dev libunwind-dev liblzma-dev libelf1 libelf-dev sbcl`
2. `git clone https://github.com/clasp-developers/clasp.git`
3. `cd clasp`
4. `./waf configure` Should that fail with "TabError: inconsistent use of tabs and spaces in indentation", you need python2 waf configure (for all following ./waf instructions as well)
5. `./waf build_cboehm`

If `./waf configure` fails it is because clasp can't find the llvm-config executable or some other dependency.
Try:

1. `cp wscript.config.template wscript.config`
2. Edit the wscript.config file and change the LLVM_CONFIG_BINARY to point to the llvm-config binary (eg: LLVM_CONFIG_BINARY = '/usr/local/opt/llvm/bin/llvm-config').

A detailed log of the build is available in `build/boehm/build.log`.

### Limit resource usage

The build needs a lot of resources (at least 8G RAM), so you may want to build as follows.

Compile in parallel, with low priority, and limit parallelism to the number of actual cores your CPU has (as opposed to hyperthreading that adds relatively little extra performance but the extra memory load of a full build thread):

1. `nice -n 19 ionice --class 3 ./waf --jobs 2 build_fboehm`

Then link on a single thread to limit peak memory usage:

2. `nice -n 19 ionice --class 3 ./waf --jobs 1 build_cboehm`

If you don't have any swap space, then your machine will very suddenly become unresponsive when the memory gets full. I suggest to do have swap, but configure linux to `vm.swappiness=1`. This way you'll have more time to intervene when it happens.

## macOS
You'll need Xcode and Homebrew for some of the extra packages that are needed to build everything. Clasp requires a specific release of llvm and does not use the Xcode version although it does use Xcode files. Otherwise things are much the same as on the Linux side -- hopefully. Adapt paths as necessary.

### Build Clasp 
0. Install homebrew from https://brew.sh/
1. `brew install cmake llvm@6 libffi bdw-gc gmp boost libunwind-headers sbcl`
2. `git clone https://github.com/clasp-developers/clasp.git`
3. If on mojave, after reinstalling xcode execute `/Library/Developer/CommandLineTools/Packages/macOS_SDK_headers_for_macOS_10.14.pkg`, to get the usual headers
4. `cd clasp`
5. `./waf configure`
6. `./waf build_cboehm`
7. If you know that the MPS version is supported then build the MPS version:  `./waf build_cmps`

If ./waf configure fails, see hint in linux instructions.

The executables will be in clasp/build/boehm/cclasp-boehm and clasp/build/mps/cclasp-mps

# Older instructions
If there are still problems - try reinstalling Xcode command line tools and start again.

Use:     `xcode-select --install`

# Clasp v0.5
## Linux
Adapt paths as necessary. 

1. Debian/Ubuntu: `apt install gcc g++ llvm clang cmake libgc-dev libgmp-dev binutils-gold binutils-dev zlib1g-dev libncurses-dev libboost-filesystem-dev libboost-regex-dev libboost-date-time-dev libboost-program-options-dev libboost-system-dev libboost-iostreams-dev`
2. `git clone -b master https://github.com/drmeister/externals-clasp ~/externals-clasp`
3. `git clone -b testing https://github.com/drmeister/clasp ~/clasp`
4. `cd ~/externals-clasp && make`
5. `cd ~/clasp && echo "LLVM_CONFIG_BINARY = '$HOME/externals-clasp/build/release/bin/llvm-config'" > wscript.config`
6. `./waf update_submodules configure build_cboehm`

## OS X
You'll need XCode and Homebrew for some of the extra packages that are needed to build everything. Otherwise things are much the same as on the Linux side -- hopefully. Adapt paths as necessary.

### Install externals-clasp until the builtin Xcode can build clasp (Xcode could not as of Sep 6, 2017)
1. `git clone https://github.com/drmeister/externals-clasp ~/externals-clasp`
2. `cd ~/externals-clasp && make`

#### Informing the new clang about the Xcode resource path
#### These instructions worked most recently on Sep 6, 2017 (drmeister) 

3. `mv ~/externals-clasp/build/release/include/c++/v1 ~/externals-clasp/build/release/include/c++/v1-original`
4. `ln -s /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/include/c++/v1 ~/externals-clasp/build/release/include/c++/v1`

Note: The Xcode path may change from time to time when OS X/Xcode releases are made.

5. `brew install gmp bdw-gc cmake boost libunwind-headers`
6. `git clone https://github.com/drmeister/clasp ~/clasp`
7. `cd ~/clasp && echo "LLVM_CONFIG_BINARY = '$HOME/externals-clasp/build/release/bin/llvm-config'" > wscript.config`
8. `./waf update_submodules configure build_cboehm`

If there are still problems - try reinstalling Xcode command line tools and start again.

Use:     `xcode-select --install`

# Clasp v0.4
# OS X
These instructions have not been tested on El Capitan

1. Clone and build [externals-clasp](http://github.com/drmeister/externals-clasp).
1. Clone and build [clasp](http://github.com/drmeister/clasp)
1. In the top clasp directory copy local.config.template into local.config
1. Edit the local.config file to uncomment #export EXTERNALS_CLASP_DIR and set it to where you installed externals-clasp
eg:<br>
`export EXTERNALS_CLASP_DIR=/Users/meister/Development/externals-clasp`
1. Build clasp with "make clean; make"

Clasp uses a lot of bleeding edge features of llvm/clang and llvm/clang is a moving target - so Clasp is tied to a specific release of llvm/clang. To ease building I've created [externals-clasp](http://github.com/drmeister/externals-clasp). It contains the specific release of llvm/clang needed by clasp.

## The following is old set up information that I may or may not need to move up into the instructions
The open source clang may need to be informed where the OS X compiler resources (header files) are located.

So the following symbolic link needs to be installed.

Let's say the external clang executable is installed at `/Users/meister/Development/externals-clasp/build/release/bin/clang`.

Then use:  

     $ mv /Users/meister/Development/externals-clasp/build/release/include/c++/v1 /Users/meister/Development/externals-clasp/build/release/include/c++/v1-original  
     $ ln -s /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/include/c++/v1 /Users/meister/Development/externals-clasp/build/release/include/c++/v1

Note: The path `/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/include/c++/v1` may change from time to time when OS X/Xcode releases are made.

## For Xcode 7.0  SDK files were moved
I changed this line in clasp/Jamroot.jam

&lt;target-os&gt;darwin:&lt;cxxflags&gt;"-isysroot /Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX10.10.sdk"

to 

&lt;target-os&gt;darwin:&lt;cxxflags&gt;"-isysroot /Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX10.11.sdk"

# LTS Ubuntu 14.04
```
sudo apt-get install git clang-3.6 clang-3.6-dev llvm-3.6 llvm-3.6-dev libboost-dev libboost-filesystem-dev libboost-date-time-dev libboost-serialization-dev libboost-iostreams-dev libboost-program-options-dev libboost-random-dev libboost-regex-dev libboost-system-dev libgmp-dev autoconf automake libtool libexpat-dev libz-dev libncurses-dev libreadline-dev libedit-dev sbcl --no-install-recommends -y
```
```bash
git clone https://github.com/drmeister/clasp.git
```
To build clasp using 4 processors
```bash
cd clasp
git checkout testing
make clean; PJOBS=4 make
````