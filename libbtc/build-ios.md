# Building libbtc to target iOS

## Make sure it builds for macOS first

Clone the master branch from the repository.

    git clone https://github.com/libbtc/libbtc
    cd libbtc

Install dependencies, configure and build with default options and tests.

    brew install autoconf automake libtool libevent
    ./autogen.sh
    ./configure
    make check

Try the tool.

    ./bitcointool --version

## Cross-compile for iOS and ARM

Clone again to a different folder.

    git clone https://github.com/libbtc/libbtc libbtc.ios
    cd libbtc.ios

Backup the original autoconf script.

    cp -p configure.ac configure.ac.original

Patch the autoconf file to disable module recovery and building of the libsecp256k1 dependency which is included as a git subtree.

    cat << 'EOF' | patch -uN configure.ac
    --- configure.ac	2021-04-22 19:32:00.000000000 +0100
    +++ configure.ac.modif	2021-04-22 19:09:01.000000000 +0100
    @@ -134,8 +134,8 @@
     AM_CONDITIONAL([WITH_NET], [test "x$with_net" = "xyes"])
     AM_CONDITIONAL([WITH_LOGDB], [test "x$with_tools" = "xyes" -o "x$with_wallet" = "xyes"])

    -ac_configure_args="${ac_configure_args} --enable-module-recovery"
    -AC_CONFIG_SUBDIRS([src/secp256k1])
    +#ac_configure_args="${ac_configure_args} --enable-module-recovery"
    +#AC_CONFIG_SUBDIRS([src/secp256k1])

     dnl make sure nothing new is exported so that we don't break the cache
     PKGCONFIG_PATH_TEMP="$PKG_CONFIG_PATH"
    EOF

Check the patch was applied.

    diff configure.ac.original configure.ac 

The diff output should just show the two commented-out lines.

    137,138c137,138
    < ac_configure_args="${ac_configure_args} --enable-module-recovery"
    < AC_CONFIG_SUBDIRS([src/secp256k1])
    ---
    > #ac_configure_args="${ac_configure_args} --enable-module-recovery"
    > #AC_CONFIG_SUBDIRS([src/secp256k1])

To generate a patch use `diff -uN configure.ac.original configure.ac` instead.

Configure the main library for iOS using minimum configuration. Wallet and network support can optionally be enabled on this step.

    ./autogen.sh
    ./configure --disable-wallet --disable-tools --disable-net \
      --disable-shared --host=arm-apple-darwin \
      CFLAGS="-O3 -arch arm64 -fembed-bitcode -isysroot `xcrun -sdk iphoneos --show-sdk-path` -mios-version-min=14.4"
      
We won't use `make` just yet as we need to manually build our dependency since we disabled it from the configuration script.

### Build the bundled libsecp256k1

    cd src/secp256k1

    ./autogen.sh

    # Minimum configuration required to work with libbtc:
    #   --enable-module-recovery --disable-tests --disable-benchmark --disable-ecmult-static-precomputation

    ./configure --enable-module-recovery --disable-tests --disable-benchmark --disable-ecmult-static-precomputation \
      --disable-shared \
      --host=arm-apple-darwin \
      CFLAGS="-O3 -arch arm64 -fembed-bitcode -isysroot `xcrun -sdk iphoneos --show-sdk-path` -mios-version-min=14.4"

    # Maximum
    #   --enable-module-recovery  --enable-module-ecdh --disable-tests --disable-benchmark

    ./configure --enable-module-recovery --enable-module-ecdh --disable-tests --disable-benchmark \
      --disable-shared \
      --host=arm-apple-darwin \
      CFLAGS="-O3 -arch arm64 -fembed-bitcode -isysroot `xcrun -sdk iphoneos --show-sdk-path` -mios-version-min=14.4"

Again do not `make` just yet.

### Finish up building the main library

    cd ../..
    make

    ls .libs
    # libbtc.a	libbtc.la	libbtc.lai

    ls src/secp256k1/.libs
    # libsecp256k1.a		libsecp256k1.la		libsecp256k1.lai
