# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NPM_NAME:=megahash
PKG_NAME:=node-$(PKG_NPM_NAME)
PKG_VERSION:=1.0.4
PKG_RELEASE:=2

PKG_SOURCE:=$(PKG_NPM_NAME)-$(PKG_VERSION).tgz
PKG_SOURCE_URL:=https://registry.npmjs.org/$(PKG_NPM_NAME)/-/
PKG_HASH:=366da36bd8368c30265bb1b3f0528905b9a6b0a5d3aec2cb16cacc66bf271bae

PKG_MAINTAINER:=Hirokazu MORIKAWA <morikw2@gmail.com>
PKG_LICENSE:=MIT
PKG_LICENSE_FILES:=LICENSE.md

PKG_BUILD_DEPENDS:=node/host
PKG_BUILD_PARALLEL:=1
PKG_USE_MIPS16:=0

include $(INCLUDE_DIR)/package.mk

define Package/node-megahash
  SUBMENU:=Node.js
  SECTION:=lang
  CATEGORY:=Languages
  TITLE:=A super-fast C++ hash table with Node.js wrapper.
  URL:=https://www.npmjs.org/package/megahash
  DEPENDS:=+node
endef

define Package/node-megahash/description
 MegaHash is a super-fast C++ hash table with a Node.js wrapper, capable of storing over 1 billion keys, has read/write speeds above 500,000 keys per second (depending on CPU speed and total keys in hash), and relatively low memory overhead. This module is designed primarily as a replacement for ES6 Maps, which seem to crash Node.js after about 15 million keys.
endef

TAR_OPTIONS+= --strip-components 1
TAR_CMD=$(HOST_TAR) -C $(1) $(TAR_OPTIONS)

NODEJS_CPU:=$(subst powerpc,ppc,$(subst aarch64,arm64,$(subst x86_64,x64,$(subst i386,ia32,$(ARCH)))))
TMPNPM:=$(shell mktemp -u XXXXXXXXXX)

TARGET_CFLAGS+=$(FPIC)
TARGET_CPPFLAGS+=$(FPIC)

define Build/Compile
	$(MAKE_VARS) \
	$(MAKE_FLAGS) \
	npm_config_arch=$(NODEJS_CPU) \
	npm_config_target_arch=$(NODEJS_CPU) \
	npm_config_build_from_source=true \
	npm_config_nodedir=$(STAGING_DIR)/usr/ \
	npm_config_prefix=$(PKG_INSTALL_DIR)/usr/ \
	npm_config_cache=$(TMP_DIR)/npm-cache-$(TMPNPM) \
	npm_config_tmp=$(TMP_DIR)/npm-tmp-$(TMPNPM) \
	npm install -g $(PKG_BUILD_DIR)
	rm -rf $(TMP_DIR)/npm-tmp-$(TMPNPM)
	rm -rf $(TMP_DIR)/npm-cache-$(TMPNPM)
endef

define Package/node-megahash/install
	$(INSTALL_DIR) $(1)/usr/lib/node/$(PKG_NPM_NAME)
	$(INSTALL_DIR) $(1)/usr/lib/node/$(PKG_NPM_NAME)/build/Release
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/node_modules/$(PKG_NPM_NAME)/{package.json,*.md} \
		$(1)/usr/lib/node/$(PKG_NPM_NAME)/
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/node_modules/$(PKG_NPM_NAME)/{binding.gyp,*js} \
		$(1)/usr/lib/node/$(PKG_NPM_NAME)/
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/node_modules/$(PKG_NPM_NAME)/node_modules \
		$(1)/usr/lib/node/$(PKG_NPM_NAME)/
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/node_modules/$(PKG_NPM_NAME)/build/Release/megahash.node \
		$(1)/usr/lib/node/$(PKG_NPM_NAME)/build/Release/
	$(INSTALL_DIR) $(1)/usr/lib/node_modules
	$(LN) ../node/$(PKG_NPM_NAME) $(1)/usr/lib/node_modules/$(PKG_NPM_NAME)
endef

define Package/node-megahash/postrm
#!/bin/sh
rm /usr/lib/node_modules/megahash || true
rm -rf /usr/lib/node/megahash || true
endef

$(eval $(call BuildPackage,node-megahash))
