# Building libbtc for iOS

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

Good. We are able to build and run on macOS. Now let's build it for our original target, iOS.

## Build as static library for iOS

We can leave our Mac version in place and clone the repo again onto a different folder.

    git clone https://github.com/libbtc/libbtc libbtc.ios
    cd libbtc.ios

### Patching the autoconf script

Backup the original autoconf script since we are going to modify it slightly.

    cp -p configure.ac configure.ac.original

Patch the autoconf file to disable module recovery and to avoid building the `libsecp256k1` dependency which is included as a git subtree.

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

To generate a new patch use `diff -uN configure.ac.original configure.ac` instead.

### Build for iOS

Configure the main library for iOS using minimum configuration. Wallet and network support can optionally be enabled on this step.
Set the CPU architecture for ARM. 

    ./autogen.sh
    ./configure --disable-wallet --disable-tools --disable-net \
      --disable-shared --host=arm64-apple-darwin \
      CFLAGS="-O3 -arch arm64 -fembed-bitcode -isysroot `xcrun -sdk iphoneos --show-sdk-path` -mios-version-min=14.4"
      
We won't use `make` just yet as we need to manually build our dependency since we disabled it from the configuration script. Let's build the bundled libsecp256k1.

    cd src/secp256k1
    ./autogen.sh

    # Minimum configuration required to work with libbtc:
    #   --enable-module-recovery --disable-tests --disable-benchmark --disable-ecmult-static-precomputation
    #
    # Maximum:
    #   --enable-module-recovery  --enable-module-ecdh --disable-tests --disable-benchmark
    ./configure --enable-module-recovery --disable-tests --disable-benchmark --disable-ecmult-static-precomputation --disable-shared \
        --host=arm64-apple-darwin \
        CFLAGS="-O3 -arch arm64 -fembed-bitcode -isysroot `xcrun -sdk iphoneos --show-sdk-path` -mios-version-min=14.4"

Again do not `make` just yet. Let's finish up building the main library

    cd ../..
    make

Check the output files.

    ls .libs
    '# libbtc.a	libbtc.la	libbtc.lai
    ls src/secp256k1/.libs
    '# libsecp256k1.a		libsecp256k1.la		libsecp256k1.lai

Prepare to create additional versions for all supported architectures and operating systems.

    mv .libs .libs_arm64_ios
    mv src/secp256k1/.libs src/secp256k1/.libs_arm64_ios
    make clean
    
### Build for simulator

Simulator supports two CPU architectures: `x86_64` and `arm64` on Apple Silicon Macs. Let's start with Intel.

    cd src/secp256k1
    ./configure --enable-module-recovery --disable-tests --disable-benchmark --disable-ecmult-static-precomputation --disable-shared \
        --host=x86_64-apple-darwin \
        CFLAGS="-O3 -arch x86_64 -fembed-bitcode -isysroot `xcrun -sdk iphonesimulator --show-sdk-path` -miphonesimulator-version-min=14.4"

    cd ../..
    ./configure --disable-wallet --disable-tools --disable-net \
        --disable-shared --host=x86_64-apple-darwin \
        CFLAGS="-O3 -arch x86_64 -fembed-bitcode -isysroot `xcrun -sdk iphonesimulator --show-sdk-path` -miphonesimulator-version-min=14.4"
    make
    mv .libs .libs_x86_64_iphonesimulator
    mv src/secp256k1/.libs src/secp256k1/.libs_x86_64_iphonesimulator
    make clean
    
Now build for simulator on `arm64`.

    cd src/secp256k1
    ./configure --enable-module-recovery --disable-tests --disable-benchmark --disable-ecmult-static-precomputation --disable-shared \
        --host=arm64-apple-darwin \
        CFLAGS="-O3 -arch arm64 -fembed-bitcode -isysroot `xcrun -sdk iphonesimulator --show-sdk-path` -miphonesimulator-version-min=14.4"

    cd ../..
    ./configure --disable-wallet --disable-tools --disable-net --disable-shared \
        --host=arm64-apple-darwin \
        CFLAGS="-O3 -arch arm64 -fembed-bitcode -isysroot `xcrun -sdk iphonesimulator --show-sdk-path` -miphonesimulator-version-min=14.4"
    make
    mv .libs .libs_arm64_iphonesimulator
    mv src/secp256k1/.libs src/secp256k1/.libs_arm64_iphonesimulator
    make clean

### Verifying library outputs

We can check the platform for the simulator libraries is `7`. For real iOS devices it should be `2`. 

    otool -l .libs_x86_64_iphonesimulator/libbtc.a | grep "platform"
    #  platform 7
    otool -l .libs_arm64_iphonesimulator/libbtc.a | grep "platform"
    #  platform 7
    otool -l .libs_arm64_ios/libbtc.a | grep "platform"
    #  platform 2

### Creating an XCFramework

We can't use `lipo` to create a fat library.

    lipo .libs_arm64_ios/libbtc.a .libs_arm64_iphonesimulator/libbtc.a .libs_x86_64_iphonesimulator/libbtc.a -create -output libbtc.a
    # fatal error: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/lipo: .libs_arm64_ios/libbtc.a and .libs_arm64_iphonesimulator/libbtc.a have the same architectures (arm64) and can't be in the same fat output file

So we try to create an XCFramework instead. No luck.

    xcodebuild -create-xcframework -library .libs_arm64_ios/libbtc.a -library .libs_arm64_iphonesimulator/libbtc.a -library .libs_x86_64_iphonesimulator/libbtc.a -output libbtc.xcframework 
    # Both 'ios-x86_64-simulator' and 'ios-arm64-simulator' represent two equivalent library definitions.

We'll then try a combination of the two. First we use lipo to combine the two simulator libraries. Then we create a XCFramework with the new simulator fat library and the iOS device version of the library.

    mkdir .libs_iphonesimulator
    lipo .libs_arm64_iphonesimulator/libbtc.a .libs_x86_64_iphonesimulator/libbtc.a -create -output .libs_iphonesimulator/libbtc.a
    xcodebuild -create-xcframework -library .libs_arm64_ios/libbtc.a -library .libs_iphonesimulator/libbtc.a -output libbtc.xcframework
    # xcframework successfully written out to: /Users/d/Developer/gh/third-party/libbtc.ios/libbtc.xcframework

Success. We can inspect the contents of the framework and see that the correct architecture, platform and platform variant values have been specified.

    ls -R libbtc.xcframework
    # Info.plist            ios-arm64            ios-arm64_x86_64-simulator
    # 
    # libbtc.xcframework/ios-arm64:
    # libbtc.a
    # 
    # libbtc.xcframework/ios-arm64_x86_64-simulator:
    # libbtc.a
    
    cat libbtc.xcframework/Info.plist
    # … 
    # <plist version="1.0"><dict>
    #     <key>AvailableLibraries</key>
    #     <array>
    #         <dict>
    #             <key>LibraryIdentifier</key>
    #             <string>ios-arm64_x86_64-simulator</string>
    #             <key>LibraryPath</key>
    #             <string>libbtc.a</string>
    #             <key>SupportedArchitectures</key>
    #             <array>
    #                 <string>arm64</string>
    #                 <string>x86_64</string>
    #             </array>
    #             <key>SupportedPlatform</key>
    #             <string>ios</string>
    #             <key>SupportedPlatformVariant</key>
    #             <string>simulator</string>
    #         </dict>
    #         <dict>
    #             <key>LibraryIdentifier</key>
    #             <string>ios-arm64</string>
    #             <key>LibraryPath</key>
    #             <string>libbtc.a</string>
    #             <key>SupportedArchitectures</key>
    #             <array>
    #                 <string>arm64</string>
    #             </array>
    #             <key>SupportedPlatform</key>
    #             <string>ios</string>
    #         </dict>
    #     </array>
    # …


#### Repeat but for `libsecp256k1`

We'll need to again use `lipo` and `xcodebuild -create-xcframework`.

    cd src/secp256k1
    mkdir .libs_iphonesimulator
    lipo .libs_arm64_iphonesimulator/libsecp256k1.a .libs_x86_64_iphonesimulator/libsecp256k1.a -create -output .libs_iphonesimulator/libsecp256k1.a
    xcodebuild -create-xcframework -library .libs_arm64_ios/libsecp256k1.a -library .libs_iphonesimulator/libsecp256k1.a -output ../../libsecp256k1.xcframework
    # xcframework successfully written out to: /Users/d/Developer/gh/third-party/libbtc.ios/libsecp256k1.xcframework
