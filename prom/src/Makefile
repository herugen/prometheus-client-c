
CFLAGS	?= -g
WARNING_CFLAGS ?=  -Wall -Wextra -Wformat=2 -Wno-format-nonliteral
LDFLAGS ?=

# Include ../include for public headers and . for private headers.
LOCAL_CFLAGS = $(WARNING_CFLAGS) -I. -I../include
LOCAL_LDFLAGS =

ifdef DEBUG
LOCAL_CFLAGS += -g3
endif

# To compile as a shared library:
ifdef SHARED
# all code is position-indep with mingw, avoid warning about useless flag
LOCAL_CFLAGS += -fPIC -fpic
endif

SOEXT_TLS=so.14
SOEXT_X509=so.1
SOEXT_CRYPTO=so.7

# Set AR_DASH= (empty string) to use an ar implementation that does not accept
# the - prefix for command line options (e.g. llvm-ar)
AR_DASH ?= -

ARFLAGS = $(AR_DASH)src

DLEXT ?= so

OBJS_PROM= \
	   prom_metric_formatter.o \
	   prom_metric.o \
	   prom_map.o \
	   prom_linked_list.o \
	   prom_histogram_buckets.o \
	   prom_histogram.o \
	   prom_gauge.o \
	   prom_counter.o \
	   prom_collector_registry.o \
	   prom_collector.o \
	   prom_string_builder.o \
	   prom_procfs.o \
	   prom_process_stat.o \
	   prom_process_limits.o \
	   prom_process_fds.o \
	   prom_metric_sample_histogram.o \
	   prom_metric_sample.o \
	   # This line is intentionally left blank

.SILENT:

.PHONY: all static shared clean

ifndef SHARED
all: static
else
all: shared static
endif

static: libprom.a

shared: libprom.$(DLEXT)

libprom.a: $(OBJS_PROM)
	echo "  AR    $@"
	$(AR) $(ARFLAGS) $@ $(OBJS_PROM)

libprom.$(SOEXT_TLS): $(OBJS_PROM)
	echo "  LD    $@"
	$(CC) -shared -Wl,-soname,$@ -o $@ $(OBJS_TLS) $(LOCAL_LDFLAGS) $(LDFLAGS)

libprom.so: libprom.$(SOEXT_TLS)
	echo "  LN    $@ -> $<"
	ln -sf $< $@

.c.o:
	echo "  CC    $<"
	$(CC) $(LOCAL_CFLAGS) $(CFLAGS) -o $@ -c $<

clean:
	rm -f *.o libprom*