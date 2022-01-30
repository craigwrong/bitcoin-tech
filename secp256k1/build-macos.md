# Building secp256k1 for macOS

## Make sure it builds for Linux

Clone the master branch from the repository.

    git clone https://github.com/craigwrong/secp256k1
    cd secp256k1
    ./docker-build.sh

For macOS, first install dependencies.

    brew install autoconf automake

We can leave our Mac version in place and clone the repo again onto a different folder.

    git clone https://github.com/bitcoin-core/secp256k1
    cp -Rp secp256k1 secp256k1.x86_64
    cp -Rp secp256k1 secp256k1.arm64

    cd src/secp256k1.x86_64
    ./autogen.sh
    # Optional MACOSX_DEPLOYMENT_TARGET=12.0
    ./configure --prefix=$PWD/dist --enable-experimental  --enable-module-extrakeys --enable-module-schnorrsig --disable-module-recovery --disable-module-ecdh --enable-tests --disable-benchmark --disable-shared \
        --host=x86_64-apple-darwin \
        CFLAGS="-O3 -arch x86_64 -fembed-bitcode -mmacosx-version-min=12.0"
    make check
    make install


Set the CPU architecture for ARM. 

    cd secp256k1.arm64
    ./autogen.sh
    ./configure --prefix=$PWD/dist --enable-experimental  --enable-module-extrakeys --enable-module-schnorrsig --disable-module-recovery --disable-module-ecdh --disable-tests --disable-benchmark --disable-shared \
        --host=arm64-apple-darwin \
        CFLAGS="-O3 -arch arm64 -fembed-bitcode -mmacosx-version-min=12.0"
    make install

### Creating an XCFramework

Create and distribute.

    lipo secp256k1.x86_64/dist/lib/libsecp256k1.a secp256k1.arm64/dist/lib/libsecp256k1.a -create -output libsecp256k1.a
    xcodebuild -create-xcframework -library libsecp256k1.a -headers secp256k1.x86_64/dist/include -output secp256k1.xcframework
    zip -Xr libsecp256k1.xcframework.zip libsecp256k1.xcframework
    touch Package.swift
    swift package compute-checksum libsecp256k1.xcframework.zip
    # Result goes into README.md.
    rm -rf Package.swift
