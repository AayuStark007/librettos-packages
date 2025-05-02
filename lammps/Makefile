include ../Makefile.inc

UPSTREAM=https://download.lammps.org/tars/lammps-17Nov2016.tar.gz
TARBALL=$(notdir $(UPSTREAM))

CC=$(RUMPRUN_CC) -L${RUMPRUN_SYSROOT}/../../obj-amd64-nrk/lib/libunwind/
CXX=$(RUMPRUN_CXX) -L${RUMPRUN_SYSROOT}/../../obj-amd64-nrk/lib/libunwind/
LINK=$(RUMPRUN_CXX) -L${RUMPRUN_SYSROOT}/../../obj-amd64-nrk/lib/libunwind/
ARCHIVE=$(RUMPRUN_TOOLCHAIN_TUPLE)-ar
export CC CXX LINK ARCHIVE RUMPRUN_PKGS_DIR

OPENMP_LIB=../openmp/libgomp.a

all: $(RUMPRUN_PKGS_DIR)/lib/libevent.a $(RUMPRUN_PKGS_DIR)/lib/libgomp.a build/configure build/Makefile_omp images

.NOTPARALLEL: $(RUMPRUN_PKGS_DIR)/lib/libevent.a
$(RUMPRUN_PKGS_DIR)/lib/libevent.a:
	$(MAKE) -C ../libevent

.NOTPARALLEL: $(RUMPRUN_PKGS_DIR)/lib/libgomp.a
$(RUMPRUN_PKGS_DIR)/lib/libgomp.a:
	$(MAKE) -C ../openmp

.PHONY: build/Makefile
build/Makefile:
	(cd build/src; $(MAKE) serial)

.PHONY: build/Makefile_omp
build/Makefile_omp: build/prepare_Omp
	(cd build/src; $(MAKE) omp_nrk)

dl/$(TARBALL):
	mkdir -p dl
	../scripts/fetch.sh ${UPSTREAM} dl/$(TARBALL)

build/configure:
	git clone --depth=1 https://github.com/AayuStark007/lammps.git -b lmp2016 build

build/prepare_Stubs:
	(cd build/src/STUBS; make clean && make)

build/prepare_Omp: build/prepare_Stubs
	(cd build/src; $(MAKE) yes-user-omp)
	(cd build/src; sh ./USER-OMP/hack_openmp_for_pgi.sh)

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
