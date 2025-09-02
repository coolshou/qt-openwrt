#
# Copyright (C) 2013 Riccardo Ferrazzo <f.riccardo87@gmail.com>
# Copyright (C) 2017 Paweł Knioła <pawel.kn@gmail.com>
# Copyright (C) 2019 Yaroslav Petrov <info@lank.me>
#
# This is free software, licensed under the GNU General Public License v3.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=qt6
PKG_VERSION:=6.8
PKG_RELEASE:=3
PKG_MD5SUM:=10a39c4b4af4f465c4ff0352a6d18a0a

PKG_SOURCE:=qt-everywhere-src-$(PKG_VERSION).$(PKG_RELEASE).tar.xz
PKG_SOURCE_URL:=https://download.qt.io/archive/qt/$(PKG_VERSION)/$(PKG_VERSION).$(PKG_RELEASE)/single
PKG_BUILD_DIR=$(BUILD_DIR)/qt-everywhere-src-$(PKG_VERSION).$(PKG_RELEASE)
UNPACK_CMD=xz -dc $(TOPDIR)/dl/$(PKG_SOURCE) | $(TAR) -C $(1) -xf -
PKG_BUILD_PARALLEL:=1
PKG_USE_MIPS16:=0
PKG_BUILD_DEPENDS:=libiconv-full pcre2 openssl

PKG_MAINTAINER:=Yaroslav Petrov <info@lank.me>

include $(INCLUDE_DIR)/package.mk
#include $(INCLUDE_DIR)/nls.mk
include $(INCLUDE_DIR)/cmake.mk

# not using sstrip here as this fucks up the .so's somehow
STRIP:=/bin/true
RSTRIP:= \
  NM="$(TOOLCHAIN_DIR)/bin/$(TARGET_CROSS)nm" \
  STRIP="$(STRIP)" \
  STRIP_KMOD="$(STRIP)" \
  $(SCRIPT_DIR)/rstrip.sh

define Package/qt6/Default
  SECTION:=libs
  CATEGORY:=Libraries
  SUBMENU:=Qt6
  TITLE:=qt6
  URL:=http://qt-project.org
  DEPENDS:=+librt +zlib +libstdcpp +libgcc +libpcre2-16 +glib2 +libpthread +libudev +libatomic +libopenssl3 @!LINUX_2_6
endef

define Package/qt6-core
  $(call Package/qt6/Default)
  TITLE+=core
endef

define Package/qt6-concurrent
  $(call Package/qt6/Default)
  TITLE+=concurrent
  DEPENDS+=+qt6-core
endef

define Package/qt6-network
  $(call Package/qt6/Default)
  TITLE+=network
  DEPENDS+=+qt6-core
endef

define Package/qt6-widgets
  $(call Package/qt6/Default)
  TITLE+=widgets
  DEPENDS+=+qt6-core +qt6-network +libjpeg-turbo
endef

define Package/qt6-websockets
  $(call Package/qt6/Default)
  TITLE+=websockets
  DEPENDS+=+qt6-core +qt6-network
endef

define Package/qt6-sql
  $(call Package/qt6/Default)
  TITLE+=sql
  DEPENDS+=+qt6-core
endef

define Package/qt6-xml
  $(call Package/qt6/Default)
  TITLE+=xml
  DEPENDS+=+qt6-core
endef

define Package/qt6-serialport
  $(call Package/qt6/Default)
  TITLE+=serialport
  DEPENDS+=+qt6-core
endef

define Package/qt6-serialbus
  $(call Package/qt6/Default)
  TITLE+=serialbus
  DEPENDS+=+qt6-core +qt6-network +qt6-serialport
endef

define Package/qt6-xmlpatterns
  $(call Package/qt6/Default)
  TITLE+=xmlpatterns
  DEPENDS+=+qt6-core +qt6-network
endef

define Package/qt6-test
  $(call Package/qt6/Default)
  TITLE+=test
  DEPENDS+=+qt6-core
endef

define Package/qt6-location
  $(call Package/qt6/Default)
  TITLE+=location
  DEPENDS+=+qt6-core
endef

#define Build/Prepare
#	$(INSTALL_DIR) $(PKG_BUILD_DIR)/qtbase/lib/fonts
# 	$(INSTALL_DIR) $(PKG_BUILD_DIR)/qtbase/mkspecs/linux-openwrt-g++
#	$(CP) ./files/fonts/* $(PKG_BUILD_DIR)/qtbase/lib/fonts/
# 	$(CP) ./files/qplatformdefs.h $(PKG_BUILD_DIR)/qtbase/mkspecs/linux-openwrt-g++/qplatformdefs.h
# 	$(CP) ./files/qmake.conf $(PKG_BUILD_DIR)/qtbase/mkspecs/linux-openwrt-g++/qmake.conf
# 	$(SED) 's@$$$$(TARGET_CROSS)@$(TARGET_CROSS)@g' $(PKG_BUILD_DIR)/qtbase/mkspecs/linux-openwrt-g++/qmake.conf

define Build/Configure
	( cd $(PKG_BUILD_DIR)/qt-everywhere-src-$(PKG_VERSION).$(PKG_RELEASE) ; \
		cmake -S . -B build \
            -DCMAKE_SYSTEM_NAME=Linux \
            -DCMAKE_SYSROOT=$(TOOLCHAIN_DIR) \
            -DCMAKE_INSTALL_PREFIX=/usr \
            -DCMAKE_C_COMPILER=$(TARGET_CROSS)gcc \
            -DCMAKE_CXX_COMPILER=$(TARGET_CROSS)g++ \
            -DCMAKE_AR=$(TARGET_CROSS)ar \
            -DCMAKE_RANLIB=$(TARGET_CROSS)ranlib \
            -DCMAKE_OBJCOPY=$(TARGET_CROSS)objcopy \
            -DCMAKE_FIND_ROOT_PATH=$(STAGING_DIR) \
            -DCMAKE_FIND_ROOT_PATH_MODE_PROGRAM=NEVER \
            -DCMAKE_FIND_ROOT_PATH_MODE_LIBRARY=ONLY \
            -DCMAKE_FIND_ROOT_PATH_MODE_INCLUDE=ONLY \
            -DCMAKE_C_FLAGS="$(TARGET_CFLAGS) $(EXTRA_CFLAGS)" \
            -DCMAKE_CXX_FLAGS="$(TARGET_CFLAGS) $(EXTRA_CFLAGS)" \
            -DCMAKE_EXE_LINKER_FLAGS="$(TARGET_LDFLAGS) $(EXTRA_LDFLAGS) -lpthread -lrt" \
            -DMYSQL_INCLUDE_DIR=$(STAGING_DIR)/usr/include/mysql \
            -DMYSQL_LIB_DIR=$(STAGING_DIR)/usr/lib/mysql \
            -DUSE_OPENSSL=ON \
            -DOPENSSL_ROOT_DIR=$(STAGING_DIR)/usr \
			-DQT_HOST_PATH=$(STAGING_DIR_HOST)/qt-host \
            -DQT_FEATURE_gui=OFF \
            -DQT_FEATURE_dbus=OFF \
            -DQT_FEATURE_opengl=OFF \
            -DQT_FEATURE_sql_mysql=OFF \
            -DQT_FEATURE_examples=OFF \
            -DQT_FEATURE_tests=OFF \
	)
endef

#define Build/Compile
#	$(MAKE) -j4 -C $(PKG_BUILD_DIR)/build
#endef

define Build/InstallDev
	$(MAKE) -C $(PKG_BUILD_DIR) install
	$(CP) $(PKG_BUILD_DIR)/qtbase/bin/qmake $(TOOLCHAIN_DIR)/bin/
	$(CP) $(PKG_BUILD_DIR)/qtbase/bin/qt.conf $(TOOLCHAIN_DIR)/bin/
	$(INSTALL_DIR) $(TOOLCHAIN_DIR)/bin/mkspecs/common/
	$(INSTALL_DIR) $(TOOLCHAIN_DIR)/bin/mkspecs/linux-openwrt-g++/
	$(CP) $(PKG_BUILD_DIR)/qtbase/mkspecs/common/linux.conf $(TOOLCHAIN_DIR)/bin/mkspecs/common/
	$(CP) $(PKG_BUILD_DIR)/qtbase/mkspecs/common/gcc-base-unix.conf $(TOOLCHAIN_DIR)/bin/mkspecs/common/
	$(CP) $(PKG_BUILD_DIR)/qtbase/mkspecs/common/g++-unix.conf $(TOOLCHAIN_DIR)/bin/mkspecs/common/
	$(CP) $(PKG_BUILD_DIR)/qtbase/mkspecs/linux-openwrt-g++/qmake.conf $(TOOLCHAIN_DIR)/bin/mkspecs/linux-openwrt-g++/
endef

define Package/qt6-core/install
	$(INSTALL_DIR) $(1)/usr/lib/
	$(CP) $(PKG_BUILD_DIR)/qtbase/lib/libQt6Core.so* $(1)/usr/lib/
	$(CP) $(PKG_BUILD_DIR)/qtbase/lib/libQt6Core.prl $(1)/usr/lib/
	$(CP) $(PKG_BUILD_DIR)/qtbase/lib/libQt6Core.la $(1)/usr/lib/
endef

define Package/qt6-concurrent/install
	$(INSTALL_DIR) $(1)/usr/lib/
	$(CP) $(PKG_BUILD_DIR)/qtbase/lib/libQt6Concurrent.so* $(1)/usr/lib/
	$(CP) $(PKG_BUILD_DIR)/qtbase/lib/libQt6Concurrent.prl $(1)/usr/lib/
	$(CP) $(PKG_BUILD_DIR)/qtbase/lib/libQt6Concurrent.la $(1)/usr/lib/
endef

define Package/qt6-network/install
	$(INSTALL_DIR) $(1)/usr/lib/
	$(CP) $(PKG_BUILD_DIR)/qtbase/lib/libQt6Network.so* $(1)/usr/lib/
	$(CP) $(PKG_BUILD_DIR)/qtbase/lib/libQt6Network.prl $(1)/usr/lib/
	$(CP) $(PKG_BUILD_DIR)/qtbase/lib/libQt6Network.la $(1)/usr/lib/
endef

define Package/qt6-widgets/install
	$(INSTALL_DIR) $(1)/usr/lib/
	$(INSTALL_DIR) $(1)/usr/lib/Qt/plugins/generic/
	$(INSTALL_DIR) $(1)/usr/lib/Qt/plugins/platforms/
	$(INSTALL_DIR) $(1)/usr/lib/Qt/plugins/imageformats/
	$(INSTALL_DIR) $(1)/usr/lib/fonts/
	$(CP) $(PKG_BUILD_DIR)/qtbase/lib/libQt6Gui.so* $(1)/usr/lib/
	$(CP) $(PKG_BUILD_DIR)/qtbase/lib/libQt6Gui.prl $(1)/usr/lib/
	$(CP) $(PKG_BUILD_DIR)/qtbase/lib/libQt6Gui.la 	$(1)/usr/lib/
	$(CP) $(PKG_BUILD_DIR)/qtsvg/lib/libQt6Svg.so* $(1)/usr/lib/
	$(CP) $(PKG_BUILD_DIR)/qtsvg/lib/libQt6Svg.prl $(1)/usr/lib/
	$(CP) $(PKG_BUILD_DIR)/qtsvg/lib/libQt6Svg.la $(1)/usr/lib/
	$(CP) $(PKG_BUILD_DIR)/qtbase/lib/libQt6Widgets.so* $(1)/usr/lib/
	$(CP) $(PKG_BUILD_DIR)/qtbase/lib/libQt6Widgets.prl $(1)/usr/lib/
	$(CP) $(PKG_BUILD_DIR)/qtbase/lib/libQt6Widgets.la $(1)/usr/lib/
	$(CP) $(PKG_BUILD_DIR)/qtbase/plugins/generic/*.so $(1)/usr/lib/Qt/plugins/generic/
	$(CP) $(PKG_BUILD_DIR)/qtbase/plugins/platforms/*.so $(1)/usr/lib/Qt/plugins/platforms/
	$(CP) $(PKG_BUILD_DIR)/qtbase/plugins/imageformats/*.so $(1)/usr/lib/Qt/plugins/imageformats/
	$(CP) $(PKG_BUILD_DIR)/qtbase/lib/fonts/* $(1)/usr/lib/fonts/
endef

define Package/qt6-websockets/install
	$(INSTALL_DIR) $(1)/usr/lib/
	$(CP) $(PKG_BUILD_DIR)/qtwebsockets/lib/libQt6WebSockets.so* $(1)/usr/lib/
	$(CP) $(PKG_BUILD_DIR)/qtwebsockets/lib/libQt6WebSockets.prl $(1)/usr/lib/
	$(CP) $(PKG_BUILD_DIR)/qtwebsockets/lib/libQt6WebSockets.la $(1)/usr/lib/
endef

define Package/qt6-sql/install
	$(INSTALL_DIR) $(1)/usr/lib/
	$(INSTALL_DIR) $(1)/usr/lib/Qt/plugins/sqldrivers/
	$(CP) $(PKG_BUILD_DIR)/qtbase/lib/libQt6Sql.so* $(1)/usr/lib/
	$(CP) $(PKG_BUILD_DIR)/qtbase/lib/libQt6Sql.prl $(1)/usr/lib/
	$(CP) $(PKG_BUILD_DIR)/qtbase/lib/libQt6Sql.la 	$(1)/usr/lib/
	$(CP) $(PKG_BUILD_DIR)/qtbase/plugins/sqldrivers/*.so $(1)/usr/lib/Qt/plugins/sqldrivers/
endef

define Package/qt6-xml/install
	$(INSTALL_DIR) $(1)/usr/lib/
	$(CP) $(PKG_BUILD_DIR)/qtbase/lib/libQt6Xml.so* $(1)/usr/lib/
	$(CP) $(PKG_BUILD_DIR)/qtbase/lib/libQt6Xml.prl $(1)/usr/lib/
	$(CP) $(PKG_BUILD_DIR)/qtbase/lib/libQt6Xml.la 	$(1)/usr/lib/
endef

define Package/qt6-serialport/install
	$(INSTALL_DIR) $(1)/usr/lib/
	$(CP) $(PKG_BUILD_DIR)/qtserialport/lib/libQt6SerialPort.so* $(1)/usr/lib/
	$(CP) $(PKG_BUILD_DIR)/qtserialport/lib/libQt6SerialPort.prl $(1)/usr/lib/
	$(CP) $(PKG_BUILD_DIR)/qtserialport/lib/libQt6SerialPort.la $(1)/usr/lib/
endef

define Package/qt6-serialbus/install
	$(INSTALL_DIR) $(1)/usr/lib/
	$(CP) $(PKG_BUILD_DIR)/qtserialbus/lib/libQt6SerialBus.so* $(1)/usr/lib/
	$(CP) $(PKG_BUILD_DIR)/qtserialbus/lib/libQt6SerialBus.prl $(1)/usr/lib/
	$(CP) $(PKG_BUILD_DIR)/qtserialbus/lib/libQt6SerialBus.la $(1)/usr/lib/
endef

define Package/qt6-xmlpatterns/install
	$(INSTALL_DIR) $(1)/usr/lib/
	$(CP) $(PKG_BUILD_DIR)/qtxmlpatterns/lib/libQt6XmlPatterns.so* $(1)/usr/lib/
	$(CP) $(PKG_BUILD_DIR)/qtxmlpatterns/lib/libQt6XmlPatterns.prl $(1)/usr/lib/
	$(CP) $(PKG_BUILD_DIR)/qtxmlpatterns/lib/libQt6XmlPatterns.la $(1)/usr/lib/
endef

define Package/qt6-test/install
	$(INSTALL_DIR) $(1)/usr/lib/
	$(CP) $(PKG_BUILD_DIR)/qtbase/lib/libQt6Test.so* $(1)/usr/lib/
	$(CP) $(PKG_BUILD_DIR)/qtbase/lib/libQt6Test.prl $(1)/usr/lib/
	$(CP) $(PKG_BUILD_DIR)/qtbase/lib/libQt6Test.la $(1)/usr/lib/
endef

define Package/qt6-location/install
	$(INSTALL_DIR) $(1)/usr/lib/
	$(CP) $(PKG_BUILD_DIR)/qtlocation/lib/libQt6Positioning.so* $(1)/usr/lib/
	$(CP) $(PKG_BUILD_DIR)/qtlocation/lib/libQt6Positioning.prl $(1)/usr/lib/
	$(CP) $(PKG_BUILD_DIR)/qtlocation/lib/libQt6Positioning.la $(1)/usr/lib/
endef

$(eval $(call BuildPackage,qt6-core))
$(eval $(call BuildPackage,qt6-concurrent))
$(eval $(call BuildPackage,qt6-network))
$(eval $(call BuildPackage,qt6-websockets))
$(eval $(call BuildPackage,qt6-widgets))
$(eval $(call BuildPackage,qt6-sql))
$(eval $(call BuildPackage,qt6-xml))
$(eval $(call BuildPackage,qt6-serialport))
$(eval $(call BuildPackage,qt6-serialbus))
$(eval $(call BuildPackage,qt6-xmlpatterns))
$(eval $(call BuildPackage,qt6-test))
$(eval $(call BuildPackage,qt6-location))
