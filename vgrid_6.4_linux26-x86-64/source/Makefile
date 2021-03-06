
ifeq ($(BASE_ARCH),$(EC_ARCH))
   $(info ERROR: please setup a compilation environment)
   $(error )
endif

ifeq ($(RPN_SHARED_LIB),)
   $(info ERROR: RPN_SHARED_LIB not found, please use :)
   $(info . ./setup.dot)
   $(error )
endif

ifeq ($(USE_EFENCE),1)
   LEFENCE=-lefence
endif

include version.dot

default: libdescrip.a

updlib: shared
	mkdir -p ../lib/${EC_ARCH}
	mv *.a *.so ../lib/${EC_ARCH}
	mkdir -p ../include/${EC_ARCH}
	cp *.h *.hf vgrid_version.* *.mod ../include/${EC_ARCH}

dependencies.mk: *.c *.F90 *.h *.hc
	ls *.hc *.c *.F90 | ./s.dependencies.pl > dependencies.mk 

include dependencies.mk

%.o: %.F90
	s.f90 -c -openmp $(MY_FFLAGS) $(FFLAGS) $(LEFENCE) $<

%.o: %.c
	s.cc -c -openmp $(MY_CFLAGS) $(CFLAGS) $(LEFENCE) $<

libdescrip.a: $(OBJECTS) dependencies.mk vgrid_version
	rm -f libdescrip.a && ar scru libdescrip.a *.o 

shared: libdescrip.a
	if [ $$ORDENV_PLAT != ubuntu-12.04-amd64-64 -a $$ORDENV_PLAT != aix-7.1-ppc7-64 ]; then \
	   s.f90 -shared -openmp -o libdescripshared.so *.o -lrmnshared -Wl,-rpath,$(RPN_SHARED_LIB);\
	else \
	   echo "No shared library for $$ORDENV_PLAT due to incompatibilities";\
	fi

vgrid_version:
	echo "character(len=128) :: vgrid_descriptors_version = \"VGRID $(BH_PULL_SOURCE_GIT_BRANCH) $(COMP_ARCH) $(ORDENV_PLAT) $(shell date)\"" > vgrid_version.hf
	echo                "char vgrid_descriptors_version[] = \"VGRID $(BH_PULL_SOURCE_GIT_BRANCH) $(COMP_ARCH) $(ORDENV_PLAT) $(shell date)\";" > vgrid_version.hc

clean:
	rm -f *.o *.mod

distclean: clean
	rm -f libdescrip.a libdescripshared.so vgrid_version.hc vgrid_version.hf dependencies.mk

clean_tilde:
	rm -f *~

clean_all: distclean  clean_tilde
