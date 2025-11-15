DEBUG = 0

ifeq ($(INTEL), 1)
ARCHS = x86_64
SDKVERSION = 16.2
SYSROOT = $(THEOS)/sdks/iPhoneSimulator16.2.sdk
TARGET = simulator:clang
else
ARCHS = arm64
SDKVERSION = 16.2
SYSROOT = $(THEOS)/sdks/iPhoneSimulator16.2.sdk
TARGET = simulator:clang
#CFLAGS += -DNEW_API
endif

# FULL PATH of the FLEX repo on your own machine
FLEX_ROOT = ../libflex/FLEX

# Function to convert /foo/bar to -I/foo/bar
dtoim = $(foreach d,$(1),-I$(d))

# Gather FLEX sources
SOURCES  = $(shell find $(FLEX_ROOT)/Classes -name '*.c')
SOURCES += $(shell find $(FLEX_ROOT)/Classes -name '*.m')
SOURCES += $(shell find $(FLEX_ROOT)/Classes -name '*.mm')
# Gather FLEX headers for search paths
_IMPORTS  = $(shell /bin/ls -d $(FLEX_ROOT)/Classes/*/)
_IMPORTS += $(shell /bin/ls -d $(FLEX_ROOT)/Classes/*/*/)
_IMPORTS += $(shell /bin/ls -d $(FLEX_ROOT)/Classes/*/*/*/)
_IMPORTS += $(shell /bin/ls -d $(FLEX_ROOT)/Classes/*/*/*/*/)
IMPORTS = -I$(FLEX_ROOT)/Classes/ $(call dtoim, $(_IMPORTS))

TWEAK_NAME = FLEXing
$(TWEAK_NAME)_GENERATOR = internal
$(TWEAK_NAME)_FILES = ../Tweak.xm ../SpringBoard.xm $(SOURCES)
$(TWEAK_NAME)_FRAMEWORKS = CoreGraphics UIKit ImageIO QuartzCore
$(TWEAK_NAME)_LIBRARIES = sqlite3 z
$(TWEAK_NAME)_CFLAGS += -fobjc-arc -w -Wno-unsupported-availability-guard $(IMPORTS) -g
$(TWEAK_NAME)_CCFLAGS += -std=gnu++11

include $(THEOS)/makefiles/common.mk
include $(THEOS_MAKE_PATH)/tweak.mk

uni::stage
	rm -f ./$(TWEAK_NAME).dylib
	@echo "==> Creating universal binary…"
	lipo -create \
		$(THEOS_OBJ_DIR)/arm64/*.dylib \
		$(THEOS_OBJ_DIR)/x86_64/*.dylib \
		-output $(TWEAK_NAME).dylib

	cp ./$(TWEAK_NAME).dylib /opt/simject/$(TWEAK_NAME).dylib
	cp -f ./$(TWEAK_NAME).plist /opt/simject/$(TWEAK_NAME).plist
	ldid -S /opt/simject/$(TWEAK_NAME).dylib
	/opt/simject/bin/resim

sim::stage
	rm -f /opt/simject/$(TWEAK_NAME).dylib
	rm -f /opt/simject/$(TWEAK_NAME).plist
	cp .theos/_/Library/MobileSubstrate/DynamicLibraries/$(TWEAK_NAME).dylib /opt/simject/$(TWEAK_NAME).dylib
	cp -f ./$(TWEAK_NAME).plist /opt/simject/$(TWEAK_NAME).plist
	ldid -S /opt/simject/$(TWEAK_NAME).dylib
	/opt/simject/bin/resim

clear::stage
	rm -f $(TWEAK_NAME).dylib
	rm -f /opt/simject/$(TWEAK_NAME).dylib
	rm -f /opt/simject/$(TWEAK_NAME).plist
