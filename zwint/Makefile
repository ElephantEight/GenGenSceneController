
SRCNAME = zwint.c
OBJNAME = zwint.o
OBJNAME_DEBUG = zwint_debug.o
LIBNAME = zwint.so
LIBNAME_DEBUG = zwint_debug.so

CWD = $(shell pwd)
BASE_DIR = $(realpath $(CWD)/../OpenWrt-SDK-ramips-for-linux-i486-gcc-4.6-linaro_uClibc-0.9.33.2)
STAGING_DIR = $(BASE_DIR)/staging_dir
TOOLCHAIN = $(STAGING_DIR)/toolchain-mipsel_r2_gcc-4.6-linaro_uClibc-0.9.33.2
CC = $(TOOLCHAIN)/bin/mipsel-openwrt-linux-gcc
STRIP = $(TOOLCHAIN)/bin/mipsel-openwrt-linux-strip
CFLAGS = -I$(STAGING_DIR)/target-mipsel_r2_uClibc-0.9.33.2/usr/include -fPIC -Os -Wall -mips16 -march='mips32r2'
LFLAGS = -pthread -shared -z relro
INSTALL_PATH = $(CWD)

export STAGING_DIR

all: output

$(OBJNAME): $(SRCNAME)
	@$(CC) -DDEBUG=0 -o $(OBJNAME) -c $(SRCNAME) $(CFLAGS)

$(OBJNAME_DEBUG): $(SRCNAME)
	@$(CC) -DDEBUG=1 -o $(OBJNAME_DEBUG) -c $(SRCNAME) $(CFLAGS)

$(LIBNAME): $(OBJNAME)
	$(CC) -o $(LIBNAME) -shared $(OBJNAME) $(LFLAGS)
	$(STRIP) $(LIBNAME)

$(LIBNAME_DEBUG): $(OBJNAME_DEBUG)
	$(CC) -o $(LIBNAME_DEBUG) -shared $(OBJNAME_DEBUG) $(LFLAGS)
	$(STRIP) $(LIBNAME_DEBUG)

output: $(LIBNAME_DEBUG) $(LIBNAME)
	@echo '  local zwint_so'
	@echo '  if UseDebugZWaveInterceptor then'
	@echo '    -- zwint debug version'
	@echo '    zwint_so = b642bin([['
	@base64 $(LIBNAME_DEBUG) | sed -e ' s/^\(.*\)$$/    \1/'
	@echo '    ]])'
	@echo '  else'
	@echo '    -- zwint non-debug version'
	@echo '	   zwint_so = b642bin([['
	@base64 $(LIBNAME) | sed -e ' s/^\(.*\)$$/    \1/'
	@echo '    ]])'
	@echo '  end'

clean:
	$(RM) $(LIBNAME) $(OBJNAME) $(LIBNAME_DEBUG) $(OBJNAME_DEBUG)

.PHONY: all clean output
