PKG_NAME = libaom
PKG_VERS = v2.0.0-rc1
PKG_EXT = tar.gz
PKG_DOWNLOAD_METHOD = git
PKG_GIT_HASH = 52fcf205565d5530773933cf38eb4c45357b6ad1
# Tag v2.0.0-rc1: 52fcf205565d5530773933cf38eb4c45357b6ad1
# Tag v1.0.0-errata1-avif: 4eb1e7795b9700d532af38a2d9489458a8038233
PKG_DIST_SITE = https://aomedia.googlesource.com/aom
PKG_DIST_FILE = $(PKG_NAME)-git$(PKG_GIT_HASH).$(PKG_EXT)
PKG_DIR = $(PKG_NAME)-git$(PKG_GIT_HASH)

DEPENDS = 

HOMEPAGE = https://aomedia.googlesource.com/aom/
COMMENT  = AOMedia Video 1 (AV1) is an open, royalty-free video coding format designed for video transmissions over the Internet.
LICENSE  = Royalty-free

UNSUPPORTED_ARCHS = $(ARM5_ARCHES) $(PPC_ARCHES)

CONFIGURE_TARGET = libaom_custom_configure
COMPILE_TARGET = libaom_custom_compile
INSTALL_TARGET = libaom_custom_install

NASM_PATH = $(WORK_DIR)/../../../native/nasm/work-native/install/usr/local/bin

CMAKE_ARGS = -DCMAKE_INSTALL_PREFIX=$(INSTALL_PREFIX)
CMAKE_ARGS += -DCMAKE_CROSSCOMPILING=TRUE
CMAKE_ARGS += -DCMAKE_SYSTEM_NAME=Linux
CMAKE_ARGS += -DCMAKE_C_COMPILER=$(TC_PATH)$(TC_PREFIX)gcc
CMAKE_ARGS += -DCMAKE_CXX_COMPILER=$(TC_PATH)$(TC_PREFIX)g++
CMAKE_ARGS += -DCMAKE_INSTALL_RPATH=$(INSTALL_PREFIX)/lib
CMAKE_ARGS += -DCMAKE_INSTALL_RPATH_USE_LINK_PATH=TRUE
CMAKE_ARGS += -DCMAKE_BUILD_WITH_INSTALL_RPATH=TRUE
CMAKE_ARGS += -DCONFIG_AV1_ENCODER=1 -DBUILD_SHARED_LIBS=1

include ../../mk/spksrc.cross-cc.mk

# Set working directory
PKG_WORK_DIR = $(WORK_DIR)/$(PKG_NAME)_$(PKG_VERS)-build

ifeq ($(findstring $(ARCH), $(ARM5_ARCHES)),$(ARCH))
CMAKE_ARGS += -DCROSS_COMPILE_ARM=ON -DENABLE_ASSEMBLY=OFF
CMAKE_ARGS += -DCMAKE_SYSTEM_PROCESSOR=armv5 -DAOM_TARGET_CPU=arm
endif

ifeq ($(findstring $(ARCH), $(ARM7_ARCHES)),$(ARCH))
CMAKE_ARGS += -DCMAKE_CXX_FLAGS=-fPIC -DCROSS_COMPILE_ARM=ON -DENABLE_ASSEMBLY=OFF
CMAKE_ARGS += -DCMAKE_SYSTEM_PROCESSOR=armv7 -DAOM_TARGET_CPU=arm
endif

ifeq ($(findstring $(ARCH), $(ARM8_ARCHES)),$(ARCH))
CMAKE_ARGS += -DCMAKE_CXX_FLAGS=-fPIC -DCROSS_COMPILE_ARM=ON -DENABLE_ASSEMBLY=OFF
CMAKE_ARGS += -DCMAKE_SYSTEM_PROCESSOR=aarch64 -DAOM_TARGET_CPU=arm64
endif

ifeq ($(findstring $(ARCH), $(PPC_ARCHES)),$(ARCH))
CMAKE_ARGS += -DCMAKE_C_FLAGS=-maltivec -DENABLE_ALTIVEC=OFF -DCPU_POWER8=OFF
CMAKE_ARGS += -DCMAKE_SYSTEM_PROCESSOR=ppc64 -DAOM_TARGET_CPU=ppc
endif

ifeq ($(findstring $(ARCH), $(x64_ARCHES)),$(ARCH))
DEPENDS += native/nasm cross/libnuma
ENV += PATH=$(NASM_PATH):$$PATH
ENV += AS=$(NASM_PATH)/nasm
CMAKE_ARGS += -DCMAKE_SYSTEM_PROCESSOR=x86_64 -DAOM_TARGET_CPU=x86_64
endif

ifeq ($(findstring $(ARCH), $(x86_ARCHES)),$(ARCH))
DEPENDS += native/nasm cross/libnuma
ENV += PATH=$(NASM_PATH):$$PATH
ENV += AS=$(NASM_PATH)/nasm
CMAKE_ARGS += -DENABLE_ASSEMBLY=OFF -DCMAKE_SYSTEM_PROCESSOR=x86
CMAKE_ARGS += -DCMAKE_SYSTEM_PROCESSOR=x86 -DAOM_TARGET_CPU=x86
endif

.PHONY: libaom_custom_configure

libaom_custom_configure: 
	$(RUN) rm -rf CMakeCache.txt CMakeFiles
	$(RUN) mkdir --parents $(PKG_WORK_DIR)
	cd $(PKG_WORK_DIR) && env $(ENV) cmake $(CMAKE_ARGS) $(WORK_DIR)/$(PKG_DIR)

.PHONY: libaom_custom_compile

libaom_custom_compile: 
	cd $(PKG_WORK_DIR) && env $(ENV) $(MAKE)

.PHONY: libaom_custom_install

libaom_custom_install: 
	cd $(PKG_WORK_DIR) && env $(ENV) $(MAKE) install DESTDIR=$(INSTALL_DIR)

