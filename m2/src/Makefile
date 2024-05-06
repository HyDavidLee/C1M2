#******************************************************************************
# Copyright (C) 2017 by Alex Fosdick - University of Colorado
#
# Redistribution, modification or use of this software in source or binary
# forms is permitted as long as the files maintain this copyright. Users are 
# permitted to modify this and use it to learn about the field of embedded
# software. Alex Fosdick and the University of Colorado are not liable for any
# misuse of this material. 
#
#*****************************************************************************

#------------------------------------------------------------------------------
# The purpose of this Makefile is to create a command line build system using GNU and GCC tools.
# The target embedded system is MSP432.
# This Makefile will allow for native and cross compiling an application.
# A preprocessor (*.i), assembly (*.asm), object (*.o), dependency (*.d), map (*.map) and executable (*out) files will be generated.
#
# Use: make [TARGET] [PLATFORM-OVERRIDES]
#
# Build Targets:
#      %.i: Generates preprocessed output of c implementation files
#      %.asm: Generates assembly output of cimplementation files and final output executable.
#      %.o: Generates ovject file for all c source files (does not link)
#      compile-all: Compile all object files, but don't link, has PHONY protection
#      Build: Compile all object files and link into executable. Has PHONY protection.
#      Clean: Removes all compiled files. Has PHONY protection
#
# Platform Overrides:
#      PLATFORM=HOST
#      PLATFORM=MSP432
#
#------------------------------------------------------------------------------
include sources.mk

# Platform Overrides
PLATFORM = MSP432 \
	   HOST

# Architectures Specific Flags
CPU = cortex-m4
ARCH = thumb
SPECS = nosys.specs

# Linker File
LINKER_FILE = ~/ese-coursera-course1/assessments/m2/src/msp432p401r.lds

# Compiler Flags and Defines
CC = gcc \
     arm-none-eabi-gcc 
LD = arm-none-eabi-ld
LDFLAGS = -Wl -T$(LINKER_FILE)
GENFLAGS = $(INCLUDES) \
	   -g \
	   -O0 \
	   -std=c99 \
	   -Wall \
	   -Werror

ARMFLAGS = -mcpu=$(CPU) \
	   -m$(ARCH) \
	   -march=armv7e-m \
	   -mfloat-abi=hard \
	   -mfpu=fpv4-sp-d16 \
	   --specs=$(SPECS)

CPPFLAGs = -E

#Target output file name
TARGET = c1m2

#Platform overrides
ifeq ($(PLATFORM), HOST)
	CC=gcc
	CFLAGS=$(GENFLAGS) -DHOST
	LDFLAGS=
	SOURCES=./main.c \
		./memory.c
else
	CC=arm-none-eabi-gcc
	CFLAGS=$(ARMFLAGS) $(GENFLAGS) -DMSP432
	LDFLAGS=-Wl,-Map=$(TARGET).map -Tmsp432p401r.lds
endif

#Defines variables with respective suffix
OBJS = $(SOURCES:.c=.o)
ASMS = $(SOURCES:.c=.asm)
IMPS = $(SOURCES:.c=.i)
DEPS = $(SOURCES:.c=.d)

#Compile object file without Linking
%.o : %.c
	$(CC) -c $< $(CFLAGS) -o $@

#Compile preprocessing file stop after preprocessing
%.i : %.c
	$(CC) -E  $< $(CFLAGS) -o $@

#Compile assembly file stop after compilation proper
%.asm : %.c
	$(CC) -S  $< $(CFLAGS) -o $@
	objdump -d $@ > $@.txt

#Create PHONY Target to build Target
.PHONY: build
build: $(TARGET).out

#Tells what build outputs
$(TARGET).out: $(OBJS)
	$(CC) $(OBJS) $(CFLAGS) $(LDFLAGS) -o $@

#Compiles all object files without linking
.PHONY: compile-all
compile-all: $(OBJS)

#Remove all compiled files from folder
.PHONY: clean
clean:
	rm -f $(OBJS) $(DEPS) $(ASMS) $(IMPS) $(TARGET).out $(TARGET).map
