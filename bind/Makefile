#
# Copyright (C) 2009-2012, 2015  Internet Systems Consortium, Inc. ("ISC")
#
# Permission to use, copy, modify, and/or distribute this software for any
# purpose with or without fee is hereby granted, provided that the above
# copyright notice and this permission notice appear in all copies.
#
# THE SOFTWARE IS PROVIDED "AS IS" AND ISC DISCLAIMS ALL WARRANTIES WITH
# REGARD TO THIS SOFTWARE INCLUDING ALL IMPLIED WARRANTIES OF MERCHANTABILITY
# AND FITNESS.  IN NO EVENT SHALL ISC BE LIABLE FOR ANY SPECIAL, DIRECT,
# INDIRECT, OR CONSEQUENTIAL DAMAGES OR ANY DAMAGES WHATSOEVER RESULTING FROM
# LOSS OF USE, DATA OR PROFITS, WHETHER IN AN ACTION OF CONTRACT, NEGLIGENCE
# OR OTHER TORTIOUS ACTION, ARISING OUT OF OR IN CONNECTION WITH THE USE OR
# PERFORMANCE OF THIS SOFTWARE.

# Configure and build the bind libraries for use by DHCP

include ./version.tmp
version=${MAJORVER}.${MINORVER}.${PATCHVER}${RELEASETYPE}${RELEASEVER}

# bindvar.tmp is constructed by configure, it has the paths for bind
# if GMAKE is blank the shell script couldn't find a gmake to use.
# binddir=
# GMAKE=
include ./bindvar.tmp

bindsrcdir=bind-${version}

bindconfig = --disable-kqueue --disable-epoll --disable-devpoll \
	--without-openssl --without-libxml2 --enable-exportlib \
	--with-gssapi=no --enable-threads=no  \
	--with-export-includedir=${binddir}/include \
	--with-export-libdir=${binddir}/lib

cleandirs = ./lib ./include
#cleandirs = ./lib ./include ./atf
cleanfiles = ./configure.log ./build.log ./install.log

all: bind1 bind2
#all: bind1 atf bind2

bind1:
# Extract the source from the tarball, if it hasn't been already.
	@if test -d ${bindsrcdir} ; then                    \
		echo ${bindsrcdir} already unpacked... ;    \
	else                                                \
		gunzip -c bind.tar.gz | tar xf - ;          \
	fi

	@if test -z "${GMAKE}"; then                        \
		echo "unable to find gmake" 1>&2 ;          \
		exit 1;                                     \
	fi

# Configure the export libraries
# Currently disable the epoll, devpoll and kqueue options as they
# don't interact well with the DHCP code.
# If the top-level Bind Makefile exists we skip the configuration step
# as we assume it's done and won't change.  Doing a make clean will
# reset things if necessary.
	@if test -f ${bindsrcdir}/Makefile ; then                       \
		echo Bind export libraries already configured ;         \
	else                                                            \
		echo Configuring BIND Export libraries for DHCP. ;      \
		rm -rf ${cleandirs} ${cleanfiles} ;                     \
		(cd ${bindsrcdir} &&                                    \
                 ./configure ${bindconfig} > ${binddir}/configure.log); \
	fi

atf:
# Build and copy the ATF support if not yet installed.
	@if test -d ./atf ; then				      \
		echo ATF support already installed ;		      \
	else							      \
		echo Building ATF support ;			      \
		(cd ${bindsrcdir}/unit;				      \
		 MAKE=${GMAKE} ${GMAKE} atf > ${binddir}/build.log ;  \
		 cp -rp atf ${binddir}) ;			      \
	fi

bind2:
# Build and install the export libraries
# No need to do anything if we already have something installed.
	@if test -d ${binddir}/lib ; then                                    \
		echo Bind export libraries already installed ;               \
	else                                                                 \
		echo Building BIND Export libraries - this takes some time. ;\
		(cd ${bindsrcdir}/lib/export ;                               \
		  echo building in `pwd` ;                                   \
		  MAKE=${GMAKE} ${GMAKE} >> ${binddir}/build.log) ;          \
                                                                             \
		echo Installing BIND Export libraries to ${binddir}. ;       \
		(cd ${bindsrcdir}/lib/export ;                               \
		  MAKE=${GMAKE} ${GMAKE} install > ${binddir}/install.log) ; \
	fi

clean:
	@echo Cleaning BIND export library.
	rm -rf ${bindsrcdir} ${cleandirs} ${cleanfiles}

# Include the following so that this Makefile is happy when the parent
# tries to use them.

distdir:

distclean:

install:

check:

uninstall:
