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
# <Put a Description Here>
#
# Use: make [TARGET] [PLATFORM-OVERRIDES]
#
# Build Targets:
#      <Put a description of the supported targets here>
#
# Platform Overrides:
#      <Put a description of the supported Overrides here
#
#------------------------------------------------------------------------------
include sources.mk

# Platform Overrides

# Some variables
EXE_BASE = c1m2
EXE_OUTPUT = $(EXE_BASE).out
EXE_MAP = $(EXE_BASE).map
EXE_ASSEMBLY = $(EXE_BASE).asm

#Variables derived from the SOURCES
OBJECTS:= $(SOURCES:.c=.o)
ASSEMBLY_FILES:= $(SOURCES:.c=.asm)
PREPROCESSED_FILES:= $(SOURCES:.c=.i)
DEPENDENCY_FILES:= $(SOURCES:.c=.d)

#set default flags for the HOST Target.

#Platform specific flags
ifeq ($(PLATFORM),MSP432)
	# Architectures Specific Flags
	LINKER_FILE = -T ../msp432p401r.lds
	CPU = cortex-m4
	ARCH = armv7e-m
	SPECS = nosys.specs

	# Compiler Flags and Defines
	CC = arm-none-eabi-gcc
	LD = arm-none-eabi-gcc
	LDFLAGS = -Wl,-Map=$(EXE_MAP) $(LINKER_FILE)
	CFLAGS = -Wall -Werror -g -O0 -std=c99
	CFLAGS = -mcpu=$(CPU) -march=$(ARCH) --specs=$(SPECS) -mfloat-abi=hard -mfpu=fpv4-sp-d16 -mthumb -Wall -Werror -g -O0 -std=c99 
	CPPFLAGS = -DMSP432

	#Tools 
	SIZE = arm-none-eabi-size 
	OBJECT_DUMP = arm-none-eabi-objdump
else
	# Architectures Specific Flags
	LINKER_FILE = 
	CPU = $(SHELL lscpu | grep 'Model name' | cut -f 2 -d ":" | awk '{$1=$1}1')
	ARCH = $(SHELL arch)
	SPECS = 

	# Compiler Flags and Defines
	CC = gcc
	LD = gcc
	LDFLAGS = -Wl,-Map=$(EXE_MAP)
	CFLAGS = -Wall -Werror -g -O0 -std=c99
	CPPFLAGS = -DHOST

	#Tools 
	SIZE = size
	OBJECT_DUMP = objdump
endif

#Targets

# Generate preprocessed files as output.
%.i : %.c
	$(CC) $(INCLUDES) $(CPPFLAGS) $(CFLAGS) -E -o $@ $< 

# Generate assembly files
%.asm : %.c
	$(CC) $(INCLUDES) $(CPPFLAGS) $(CFLAGS) -S -o $@ $< 

# Generate object files.
%.o : %.c
	$(CC) $(INCLUDES) $(CPPFLAGS) $(CFLAGS) -MD -c $<
	
#compile-all target
.PHONY: compile-all
compile-all: $(OBJECTS)

# full build target (generates objects *.o, dependencies (*.d), and final exe (.out) and final map (.map)
.PHONY: build
build: $(OBJECTS)
	$(LD) $(CPPFLAGS) $(CFLAGS) $(LDFLAGS) -o $(EXE_OUTPUT) $(OBJECTS)
	$(OBJECT_DUMP) -D $(EXE_OUTPUT) > $(EXE_ASSEMBLY)
	$(SIZE) ./$(EXE_OUTPUT)

# clean target
.PHONY: clean
clean:
	rm -f $(OBJECTS) $(ASSEMBLY_FILES) $(PREPROCESSED_FILES) $(DEPENDENCY_FILES) $(EXE_OUTPUT) $(EXE_ASSEMBLY) $(EXE_MAP)
	
