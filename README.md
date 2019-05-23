# expedite-setup-toolchain-for-ARM

# Background

If you are to develop for some embedded devices, most likely you have got the corresponding toolchain from your upstream vendor. But sometimes you will need your own toolchain.

# Using LLVM

If you are a kernel developer, gcc suite are still the prefered option. Othercase please go straight to LLVM.

sudo apt install llvm clang

# Prepare runtime compiler library

The ready-for-use runtime compiler library can be downloaded directly from ARM develop website or https://archlinuxarm.org/packages/arm/compiler-rt

extract the package and get the runtime compiler library: libclang_rt.builtins-armhf.a

# Find runtime compiler library default location in your system

clang -target arm-linux-gnueabihf -rtlib=compiler-rt -print-libgcc-file-name

# Prepare the runtime compiler library

cp libclang_rt.builtins-armhf.a  /usr/lib/llvm-8/lib/clang/8.0.0/lib/linux/(The previous command will give you the location)


# Prepare the libc library, keep away from GNU libc and keep static to get the most freedom

git clone //git.musl-libc.org/musl

cd musl

CC="clang -v -static -target armv7a-linux-gnueabihf --sysroot=/home/btian/crosstools -rtlib=compiler-rt -fpic" CROSS_COMPILE="llvm-" ./configure --prefix=/home/btian/crosstools/

(/home/btian/crosstools should be replaced with your own location that used to store your toolchain)

make; make install

# Cheat gcc linking script
touch crtbeginT.o crtend.o ; mv crtbeginT.o crtend.o /home/btian/crosstools/lib/

(Again, /home/btian/crosstools/lib should be replaced with your own location that used to store the libraries of your toolchain)

# Prepare linux-headers

Download kernel source code from https://cdn.kernel.org/pub/linux/kernel/v4.x/linux-4.4.180.tar.xz

make ARCH=arm headers_install INSTALL_HDR_PATH=/home/btian/crosstools/

(Once more, /home/btian/crosstools should be replaced with your own location that used to store your toolchain)

# References

https://kernelnewbies.org/KernelHeaders, Linux kernel-headers howto.
https://stackoverflow.com/questions/15165306/compile-a-static-binary-which-code-there-a-function-gethostbyname, stop using GNU libc.
https://cdn.kernel.org/, Linux kernel source code.
https://archlinuxarm.org/packages/arm/compiler-rt, compiler runtime libraries for ARM.
