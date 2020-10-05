CC ?=gcc
SYSTEMD ?= 1
QUBES_CFLAGS:=-I../libqrexec -g -O2 -Wall -Wextra -Werror -pie -fPIC \
   $(shell pkg-config --cflags vchan-$(BACKEND_VMM)) -fstack-protector \
   -D_FORTIFY_SOURCE=2 -fstack-protector-strong -std=gnu11 -D_POSIX_C_SOURCE=200809L \
   -D_GNU_SOURCE $(CFLAGS)
LDFLAGS += -pie -Wl,-z,relro,-z,now -L../libqrexec
LDLIBS += $(shell pkg-config --libs vchan-$(BACKEND_VMM)) -lqrexec-utils

ifeq ($(SYSTEMD),1)
QUBES_CFLAGS += `pkg-config --cflags libsystemd || pkg-config --cflags libsystemd-daemon`
QUBES_CFLAGS += -DHAVE_SYSTEMD
LDLIBS += `pkg-config --libs libsystemd || pkg-config --libs libsystemd-daemon`
endif

BINDIR ?= /usr/bin
LIBDIR ?= /usr/lib
SBINDIR?= /usr/sbin

MAKEFLAGS := -r
.SUFFIXES:

all: qrexec-daemon qrexec-client
clean:
	rm -f *.o *~ qrexec-daemon qrexec-client *.o.dep
install: all
	install -d $(DESTDIR)$(SBINDIR) $(DESTDIR)$(BINDIR)
	install -t $(DESTDIR)$(SBINDIR) -m 755 qrexec-daemon
	install -t $(DESTDIR)$(BINDIR) -m 755 qrexec-client
	install -d $(DESTDIR)$(LIBDIR)/qubes
	ln -s $(DESTDIR)$(BINDIR)/qrexec-client $(DESTDIR)$(LIBDIR)/qubes/qrexec-client
.PHONY: all clean install

qrexec-daemon qrexec-client: %: %.o
	$(CC) $(LDFLAGS) -pie -g -o $@ $(QUBES_CFLAGS) $< $(LDLIBS)

%.o: %.c
	$(CC) $< -c -o $@ $(QUBES_CFLAGS) -MD -MP -MF $@.dep

-include *.o.dep
