HERMITUX_BASE=/home/pierre/Desktop/hermitux

HERMIT_LOCAL_INSTALL=$(HERMITUX_BASE)/hermitux-kernel/prefix
MUSL_PREFIX=$(HERMITUX_BASE)/musl/prefix
MUSL_GCC=$(MUSL_PREFIX)/bin/musl-gcc
GLIBC_PREFIX=/home/pierre/Desktop/glibc-build/prefix

# For obfuscation, add this to the CFLAGS:
# -mllvm -fla -mllvm -sub -mllvm -bcf
#  https://github.com/obfuscator-llvm/obfuscator
CFLAGS=-g -O3
LDFLAGS=-static

SRC_C := $(shell ls *.c)
SRC_F := $(shell ls *.f)
OBJ_C=$(SRC_C:.c=.o)
OBJ_F=$(SRC_F:.f=.o)
PROG?=prog

KERNEL=$(HERMIT_LOCAL_INSTALL)/x86_64-hermit/extra/tests/hermitux
VERBOSE?=0
ISLE=uhyve
KVM=0
STRIP?=0
ARGS?=

# Can be $(MUSL_GCC) or clang (and also the obfuscated version of clang)
CC=gfortran

all: $(PROG)

$(PROG): $(OBJ_C) $(OBJ_F)
	$(CC) -static -o $(PROG) $^ $(LDFLAGS)
#ifeq ($(STRIP),1)
#	strip $(PROG)
#endif

%.o: %.c
	gcc $(CFLAGS) -c $^ -o $@

%.o: %.f
	$(CC) $(CFLAGS) -c $^ -o $@

objdump:
	objdump --source $(PROG) > /tmp/objdump.txt && vim /tmp/objdump.txt

test: $(PROG)
	HERMIT_VERBOSE=$(VERBOSE) HERMIT_ISLE=$(ISLE) HERMIT_TUX=1 \
				HERMIT_KVM=$(KVM) $(HERMIT_LOCAL_INSTALL)/bin/proxy $(KERNEL) \
				$(PROG) $(ARGS)

gdb:
	HERMIT_VERBOSE=$(VERBOSE) HERMIT_DEBUG=1 \
				HERMIT_ISLE=qemu HERMIT_KVM=0 \
				$(HERMIT_LOCAL_INSTALL)/bin/proxy $(PROG)

clean:
	rm -rf *.o $(PROG)

