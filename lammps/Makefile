include ../Makefile.inc

UPSTREAM=https://download.lammps.org/tars/lammps-20Sep2021.tar.gz # https://download.lammps.org/tars/lammps-17Nov2016.tar.gz
TARBALL=$(notdir $(UPSTREAM))

CC=$(RUMPRUN_CC) -L${RUMPRUN_SYSROOT}/../../obj-amd64-nrk/lib/libunwind/
CXX=$(RUMPRUN_CXX) -L${RUMPRUN_SYSROOT}/../../obj-amd64-nrk/lib/libunwind/
LINK=$(RUMPRUN_CXX) -L${RUMPRUN_SYSROOT}/../../obj-amd64-nrk/lib/libunwind/
ARCHIVE=$(RUMPRUN_TOOLCHAIN_TUPLE)-ar
export CC CXX LINK ARCHIVE

all: $(RUMPRUN_PKGS_DIR)/lib/libevent.a build/configure build/Makefile images

.NOTPARALLEL: $(RUMPRUN_PKGS_DI)/lib/libevent.a
$(RUMPRUN_PKGS_DIR)/lib/libevent.a:
	$(MAKE) -C ../libevent

build/stamp_patch:
	(cd build && ../../scripts/apply-patches.sh ./ ../patches/*)
	touch $@

.PHONY: build/Makefile
build/Makefile: build/stamp_patch
	(cd build/src; make serial)

dl/$(TARBALL):
	mkdir -p dl
	../scripts/fetch.sh ${UPSTREAM} dl/$(TARBALL)

build/configure: | dl/$(TARBALL)
	mkdir -p build
	(cd build && tar -xz --strip-components 1 -f ../dl/$(TARBALL))

.PHONY: images
images: images/bench.iso

images/bench.iso: $(wildcard build/bench/*)
	$(RUMPRUN_GENISOIMAGE) -o images/bench.iso build/bench

.PHONY: clean
clean:
	(cd build/src; make clean-serial)
	rm -rf images/bench.iso

.PHONY: distclean
distclean: clean
	rm -rf build
