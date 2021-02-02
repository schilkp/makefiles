# The Compiler + Compiler Flags
CC=gcc
LDFLAGS=
CFLAGS=-Wall -Wextra -Wpedantic

# Output name:
TARGET=my_c_program

# Source Directories
SRC_DIRS=src libs/lib1/src

# Include Directories
INC_DIRS=inc libs/lib1/inc

# Build Directory
BUILDDIR=build

# Dependencies Directory
DEPDIR=$(BUILDDIR)

# Generate list of all source files in SRC_DIRS:
SOURCES=$(foreach dir, $(SRC_DIRS), $(wildcard $(dir)/*.c))

# Generate list of all object files in BUILDDIR to be linked together:
OBJECTS=$(addprefix $(BUILDDIR)/,$(SOURCES:.c=.o))

# Generate list of all dependency files in DEPDIR
DEPENDENCIES=$(addprefix $(DEPDIR)/,$(SOURCES:.c=.d))

# Generate list of all headers in INC_DIRS:
HEADERS=$(foreach dir, $(INC_DIRS), $(wildcard $(dir)/*.h))

# Generate include flags from INC_DIRS, add to CFLAGS:
CFLAGS += $(addprefix -I,$(INC_DIRS))

# Flags for dependency generation
DEPFLAGS=-MMD -MP -MF $(DEPDIR)/$*.d
CFLAGS += $(DEPFLAGS)

.PHONY: all clean info

all: $(TARGET)

clean: 
	-rm -f $(TARGET)
	-rm -rf $(BUILDDIR)
	-rm -rf $(DEPDIR)

info:
	@echo Makefile Info:
	@echo 
	@echo Source Directories: $(SRC_DIRS)
	@echo Sources: $(SOURCES)
	@echo 
	@echo Include Directories: $(INC_DIRS)
	@echo Headers: $(HEADERS)
	@echo 
	@echo Build Directory: $(BUILDDIR)
	@echo .d Directory: $(DEPDIR)
	@echo 
	@echo C-Compiler: $(CC)
	@echo C-Compiler Flags: $(CFLAGS)

# Link target
$(TARGET): $(OBJECTS) makefile
	@mkdir -p $(dir $@)
	$(CC) -o $(TARGET) $(OBJECTS) $(LDFLAGS)
	@echo Successfully generated $(TARGET)!

# Compile Objects
$(BUILDDIR)/%.o: %.c makefile 
	@mkdir -p $(dir $@)
	@mkdir -p $(dir $(DEPDIR)/$*.d)
	$(CC) -c $< -o $@ $(CFLAGS) 

$(DEPENDENCIES): # Make dependencies an explicit target to prevent them getting deleted

ifneq ($(MAKECMDGOALS),clean) # Prevent regeneration of .d files on non-build targets:
ifneq ($(MAKECMDGOALS),info) 
include $(DEPENDENCIES) # Include generated dependency files, to make .o files depend on the right .h files
endif
endif 
