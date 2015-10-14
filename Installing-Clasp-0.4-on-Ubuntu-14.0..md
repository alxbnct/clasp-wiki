###Packages that are necessary to install Clasp on LTS Ubuntu 14.04.3

Start up VirtualBox 5.0.6 and download Ubuntu 14.04.03 from here:  http://www.ubuntu.com/download/server

Then do the following:

    sudo apt-get update
    sudo apt-get upgrade

    sudo apt-get git --no-install-recommends -y

    sudo apt-get install clang-3.6 clang-3.6-dev llvm-3.6 llvm-3.6-dev --no-install-recommends -y

    # Boost
    sudo apt-get install libboost-dev libboost-filesystem-dev libboost-date-time-dev libboost-serialization-dev libboost-iostreams-dev libboost-program-options-dev libboost-random-dev libboost-regex-dev libboost-system-dev -y --no-install-recommends
    # Gmp
    sudo apt-get install -y --no-install-recommends libgmp-dev
    # For building Boehm
    sudo apt-get install -y --no-install-recommends autoconf automake libtool
    # Other dependent libraries
    sudo apt-get install -y --no-install-recommends libexpat-dev libz-dev libncurses-dev libreadline-dev
    sudo apt-get install -y --no-install-recommends libedit-dev

    # Finally, to clone clasp
    git clone https://github.com/drmeister/clasp.git

    # For development
    sudo apt-get install -y --no-install-recommends emacs