include ../utils/Makefrag
# for ncursesw/term.h
export CFLAGS := $(CFLAGS) -I$(shell pwd)/../sysroot/include/ncursesw

all: download/Python-3.13.5.tar.xz
	rm -rf temp build
	mkdir -p temp build
	cd temp && tar xvf ../download/Python-3.13.5.tar.xz
	cd temp/Python-3.13.5 && mkdir build && mkdir build-native
	# a native python3 is required for cross compilation
	cd temp/Python-3.13.5/build-native && CC="cc" CXX="" LD="ld" AR="ar" CXXFLAGS="" CFLAGS="" LDFLAGS="" PKG_CONFIG_LIBDIR="" ../configure
	cd temp/Python-3.13.5/build-native && CC="cc" CXX="" LD="ld" AR="ar" CXXFLAGS="" CFLAGS="" LDFLAGS="" PKG_CONFIG_LIBDIR="" make -j $(shell nproc)
	cd temp/Python-3.13.5/build-native && CC="cc" CXX="" LD="ld" AR="ar" CXXFLAGS="" CFLAGS="" LDFLAGS="" PKG_CONFIG_LIBDIR="" make install DESTDIR=$(shell pwd)/temp/native
	# HACK: set ANDROID_API_LEVEL=1 to link the shared libraries with libpython
	cd temp/Python-3.13.5/build && ../configure ANDROID_API_LEVEL=1 --prefix=/data/app/base.org/base_1.0 --disable-test-modules --enable-shared --host aarch64-unknown-linux-musl --build=aarch64 --enable-ipv6 --with-ensurepip=install --with-build-python=$(shell pwd)/temp/native/usr/local/bin/python3 ac_cv_file__dev_ptmx=no ac_cv_file__dev_ptc=no
	# missing gettext in libintl.h
	cd temp/Python-3.13.5/build && sed -i.bak "s/#define HAVE_LIBINTL_H 1//" pyconfig.h
	cd temp/Python-3.13.5/build && make -j $(shell nproc)
	cd temp/Python-3.13.5/build && make install DESTDIR=$(shell pwd)/build
	$(OHOS_SDK_HOME)/native/llvm/bin/llvm-strip build/data/app/base.org/base_1.0/bin/python3.13
	$(OHOS_SDK_HOME)/native/llvm/bin/llvm-strip build/data/app/base.org/base_1.0/lib/*.so*
	$(OHOS_SDK_HOME)/native/llvm/bin/llvm-strip build/data/app/base.org/base_1.0/lib/python3.13/config-3.13/libpython3.13.a
	$(OHOS_SDK_HOME)/native/llvm/bin/llvm-strip build/data/app/base.org/base_1.0/lib/python3.13/lib-dynload/*.so
	mkdir -p ../sysroot
	cp -rfv build/data/app/base.org/base_1.0/. ../sysroot | tee file.lst

download/Python-3.13.5.tar.xz:
	mkdir -p download
	cd download && wget https://www.python.org/ftp/python/3.13.5/Python-3.13.5.tar.xz
