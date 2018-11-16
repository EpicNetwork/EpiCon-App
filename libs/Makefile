#====================================#

linux_CC = gcc
macosx_CC= gcc
win32_CC = i686-w64-mingw32-gcc
win64_CC = x86_64-w64-mingw32-gcc

MYDIR := $(realpath .)
UPPERDIR = $(MYDIR)/..
BUILDDIR = $(UPPERDIR)/build

LUAJIT_BUILD_DIR = $(BUILDDIR)
LPEG_BUILD_DIR = $(LUAJIT_BUILD_DIR)/share/lua/5.1
LFS_BUILD_DIR = $(LUAJIT_BUILD_DIR)/share/lua/5.1

LUAJIT_MAKE_DIR = $(MYDIR)/luajit
LPEG_MAKE_DIR = $(MYDIR)/lpeg
LFS_MAKE_DIR = $(MYDIR)/luafilesystem
LSOCK_MAKE_DIR = $(MYDIR)/luasocket

LUAJIT_RESULTS_DIR = $(LUAJIT_MAKE_DIR)
LPEG_RESULTS_DIR = $(LPEG_MAKE_DIR)
LFS_RESULTS_DIR = $(LFS_MAKE_DIR)/src
LSOCK_RESULTS_DIR = $(LSOCK_MAKE_DIR)/src

JOBS ?= 4

#====================================#

linux_EXT = .so
macosx_EXT= .so
win32_EXT = .dll
win64_EXT = .dll
EXT      ?= $(linux_EXT)

LFS_RESULTS = lfs$(EXT) lfs.h

#====================================#

MKDIR = mkdir
MKDIR_SAFE = mkdir -p

RM = rm
RM_SAFE = rm -f

RMDIR = rm -r
RMDIR_SAFE = rm -rf

CP = cp
CP_FOLDER = cp -r

#====================================#

default: linux
	

archs = linux macosx win32 win64

$(archs): build_folders
	make -j $(JOBS) CC=$($@_CC) PREFIX=$(BUILDDIR) -C $(LUAJIT_MAKE_DIR) install
	$(MKDIR_SAFE) $(LUAJIT_BUILD_DIR)/include/lua
	$(CP_FOLDER) $(LUAJIT_BUILD_DIR)/include/luajit-2.1 $(LUAJIT_BUILD_DIR)/include/lua/5.1/
	make -j $(JOBS) ARCH=$@ CC=$($@_CC) EXT=$($@_EXT) -C $(LPEG_MAKE_DIR) $@
	make -j $(JOBS) CC=$($@_CC) -C $(LSOCK_MAKE_DIR) $@
	make -j $(JOBS) CC=$($@_CC) PREFIX_anyarch=$(LUAJIT_BUILD_DIR) -C $(LSOCK_MAKE_DIR) install
	make -j $(JOBS) ARCH=$@ CC=$($@_CC) EXT=$($@_EXT) -C $(LFS_MAKE_DIR)
	#make -j $(JOBS) ARCH=$@ CC=$($@_CC) EXT=$($@_EXT) $(LFS_RESULTS)

clean: clean_build_folders
	make -j $(JOBS) -C $(LUAJIT_MAKE_DIR) clean
	make -j $(JOBS) -C $(LPEG_MAKE_DIR) clean
	make -j $(JOBS) -C $(LFS_MAKE_DIR) clean
	make -j $(JOBS) -C $(LSOCK_MAKE_DIR) clean

#====================================#

$(LFS_RESULTS): $(LFS_BUILD_DIR)
	make -C $(LFS_MAKE_DIR) $(ARCH)
	$(CP) $(LFS_RESULTS_DIR)/$@ $(LFS_BUILD_DIR)/$@

$(BUILDDIR):
	$(MKDIR_SAFE) $(BUILDDIR)

($sort $(LUAJIT_BUILD_DIR) $(LPEG_BUILD_DIR) $(LFS_BUILD_DIR)): $(BUILDDIR)
	$(MKDIR_SAFE) $@

build_folders: $(LUAJIT_BUILD_DIR) $(LPEG_BUILD_DIR) $(LFS_BUILD_DIR)

clean_build_folders:
	$(RMDIR_SAFE) $(LUAJIT_BUILD_DIR) $(LPEG_BUILD_DIR) $(LFS_BUILD_DIR) $(BUILDDIR)

#====================================#

.PHONY: build_folders $(LFS_RESULTS) clean_build_folders default $(archs) clean
