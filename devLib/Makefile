#
# Makefile:
#	wiringPi device - A "wiring" library for the Raspberry Pi
#
#	Copyright (c) 2012-2016 Gordon Henderson
#################################################################################
# This file is part of wiringPi:
#	https://projects.drogon.net/raspberry-pi/wiringpi/
#
#    wiringPi is free software: you can redistribute it and/or modify
#    it under the terms of the GNU Lesser General Public License as published by
#    the Free Software Foundation, either version 3 of the License, or
#    (at your option) any later version.
#
#    wiringPi is distributed in the hope that it will be useful,
#    but WITHOUT ANY WARRANTY; without even the implied warranty of
#    MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
#    GNU Lesser General Public License for more details.
#
#    You should have received a copy of the GNU Lesser General Public License
#    along with wiringPi.  If not, see <http://www.gnu.org/licenses/>.
#################################################################################

VERSION=$(shell cat ../VERSION)
DYN_VERS_MAJ=2
DESTDIR?=/usr
PREFIX?=/local
BASE_NAME=libwiringPiDev
STATIC=$(BASE_NAME).a
DYNAMIC=$(BASE_NAME).so.$(VERSION)

LDCONFIG?=ldconfig

ifneq ($V,1)
Q ?= @
endif

#DEBUG	= -g -O0
DEBUG	?= -O2
CC	?= gcc
INCLUDE	?= -I. -I../wiringPi
DEFS	?= -D_GNU_SOURCE
CFLAGS	?= $(DEBUG) $(DEFS) -Wformat=2 -Wall -Winline $(INCLUDE) -pipe -fPIC $(EXTRA_CFLAGS)

LIBS    =

###############################################################################

SRC	=	ds1302.c maxdetect.c  piNes.c		\
		gertboard.c piFace.c			\
		lcd128x64.c lcd.c			\
		scrollPhat.c				\
		piGlow.c

OBJ	=	$(SRC:.c=.o)

HEADERS	=	ds1302.h gertboard.h  lcd128x64.h  lcd.h  maxdetect.h piFace.h  piGlow.h  piNes.h\
		scrollPhat.h

all:		$(DYNAMIC)

static:		$(STATIC)

$(STATIC):	$(OBJ)
	$Q echo "[Link (Static)]"
	$Q ar rcs $(STATIC) $(OBJ)
	$Q ranlib $(STATIC)

$(DYNAMIC):	$(OBJ)
	$Q echo "[Link (Dynamic)]"
	$Q $(CC) -shared -Wl,-soname,$(BASE_NAME).so.$(DYN_VERS_MAJ) -o $(BASE_NAME).so.$(VERSION) -lpthread $(OBJ) $(LDFLAGS)
	$Q ln -sf $(BASE_NAME).so.$(VERSION) $(BASE_NAME).so
	$Q ln -sf $(BASE_NAME).so.$(VERSION) $(BASE_NAME).so.$(DYN_VERS_MAJ)

.c.o:
	$Q echo [Compile] $<
	$Q $(CC) -c $(CFLAGS) -fPIC $< -o $@

.PHONY:	clean
clean:
	$Q echo "[Clean]"
	$Q rm -f $(OBJ) $(OBJ_I2C) *~ core tags Makefile.bak libwiringPiDev.*

.PHONY:	tags
tags:	$(SRC)
	$Q echo [ctags]
	$Q ctags $(SRC)


.PHONY:	install
install:	$(DYNAMIC)
	@install -m 0755 -d			$(LIB_DIR)
	@install -m 0755 $(BASE_NAME).so	$(LIB_DIR)/$(DYNAMIC)
	@install -m 0755 -d			$(INCLUDE_DIR)
	@install -m 0644 $(HEADERS)		$(INCLUDE_DIR)	
	@ln -sf $(DYNAMIC)			$(LIB_DIR)/$(BASE_NAME).so
	@ln -sf $(DYNAMIC)			$(LIB_DIR)/$(BASE_NAME).so.$(DYN_VERS_MAJ)

.PHONY:	install-static
install-static:	$(STATIC)
	@install -m 0644 $(STATIC)		$(LIB_DIR)	

.PHONY: check-deb-destdir
check-deb-destdir:
ifndef DEB_DESTDIR
	$(error DEB_DESTDIR is undefined)
endif

.PHONY:	install-deb
install-deb:	$(DYNAMIC) check-deb-destdir
	$Q echo "[Install Headers: deb]"
	$Q install -m 0755 -d							$(DEB_DESTDIR)/usr/include
	$Q install -m 0644 $(HEADERS)						$(DEB_DESTDIR)/usr/include
	$Q echo "[Install Dynamic Lib: deb]"
	install -m 0755 -d							$(DEB_DESTDIR)/usr/lib
	install -m 0755 libwiringPiDev.so.$(VERSION)				$(DEB_DESTDIR)/usr/lib/libwiringPiDev.so.$(VERSION)
	ln -sf $(DEB_DESTDIR)/usr/lib/libwiringPiDev.so.$(VERSION)		$(DEB_DESTDIR)/usr/lib/libwiringPiDev.so

.PHONY:	uninstall
uninstall:
	$Q echo "[UnInstall]"
	$Q cd $(DESTDIR)$(PREFIX)/include/ && rm -f $(HEADERS)
	$Q cd $(DESTDIR)$(PREFIX)/lib/     && rm -f libwiringPiDev.*
	$Q $(LDCONFIG)


.PHONY:	depend
depend:
	makedepend -Y $(SRC)

# DO NOT DELETE

ds1302.o: ds1302.h
maxdetect.o: maxdetect.h
piNes.o: piNes.h
gertboard.o: gertboard.h
piFace.o: piFace.h
lcd128x64.o: font.h lcd128x64.h
lcd.o: lcd.h
scrollPhat.o: scrollPhatFont.h scrollPhat.h
piGlow.o: piGlow.h
