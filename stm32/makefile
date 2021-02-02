################################
### Overall Project Settings ###
################################

# Project/Target name:
TARGET=stm32_project

# Build directories: 
BUILD_DIR_ROOT=build
DEP_DIR=$(BUILD_DIR)

# Default build configuration 
# can be overwritten: make BUILD=release
BUILD ?= debug

# Seperate release/debug builds:
BUILD_DIR =  $(BUILD_DIR_ROOT)/$(BUILD)

############################
### Project Source Files ###
############################

# C Files
# Directories for automatic source discovery
C_DIRS = \
Src 
# Individual sources
C_SOURCES +=  \
Drivers/STM32F3xx_HAL_Driver/Src/stm32f3xx_hal_tim.c \
Drivers/STM32F3xx_HAL_Driver/Src/stm32f3xx_hal_tim_ex.c \
Drivers/STM32F3xx_HAL_Driver/Src/stm32f3xx_hal.c \
Drivers/STM32F3xx_HAL_Driver/Src/stm32f3xx_hal_rcc.c \
Drivers/STM32F3xx_HAL_Driver/Src/stm32f3xx_hal_rcc_ex.c \
Drivers/STM32F3xx_HAL_Driver/Src/stm32f3xx_hal_gpio.c \
Drivers/STM32F3xx_HAL_Driver/Src/stm32f3xx_hal_dma.c \
Drivers/STM32F3xx_HAL_Driver/Src/stm32f3xx_hal_cortex.c \
Drivers/STM32F3xx_HAL_Driver/Src/stm32f3xx_hal_pwr.c \
Drivers/STM32F3xx_HAL_Driver/Src/stm32f3xx_hal_pwr_ex.c \
Drivers/STM32F3xx_HAL_Driver/Src/stm32f3xx_hal_flash.c \
Drivers/STM32F3xx_HAL_Driver/Src/stm32f3xx_hal_flash_ex.c \
Drivers/STM32F3xx_HAL_Driver/Src/stm32f3xx_hal_i2c.c \
Drivers/STM32F3xx_HAL_Driver/Src/stm32f3xx_hal_i2c_ex.c \
Drivers/STM32F3xx_HAL_Driver/Src/stm32f3xx_hal_exti.c \
# Include Dirs
C_INC_DIRS =\
Inc \
Drivers/STM32F3xx_HAL_Driver/Inc \
Drivers/STM32F3xx_HAL_Driver/Inc/Legacy \
Drivers/CMSIS/Device/ST/STM32F3xx/Include \
Drivers/CMSIS/Include \

# ASM Files
# Directories for automatic file discovery
ASM_DIRS =
# Individual files
ASM_SOURCES += \
startup_stm32f303xc.s
# Include Dirs
ASM_INC_DIRS =

#########################
### Compiler Settings ###
#########################

### Compiler Setup
# Allow for a non-path gcc with 'make GCC_PATH=xx'
ifdef GCC_PATH
	PREFIX = $(GCC_PATH)/arm-none-eabi-
else
	PREFIX = arm-none-eabi-
endif

CC = $(PREFIX)gcc
AS = $(PREFIX)gcc -x assembler-with-cpp
CP = $(PREFIX)objcopy
SZ = $(PREFIX)size
HEX = $(CP) -O ihex
BIN = $(CP) -O binary -S

### MCU specific flags common to all types of sourcefiles
CPU = -mcpu=cortex-m4
FPU = -mfpu=fpv4-sp-d16
FLOAT-ABI = -mfloat-abi=hard
MCU = $(CPU) -mthumb $(FPU) $(FLOAT-ABI)

COMMON_FLAGS += $(MCU)
COMMON_FLAGS += --specs=nano.specs

# Set optimisation level depending on build configuration:
ifeq ($(BUILD),release)
	COMMON_FLAGS += -O2
else ifeq ($(BUILD),debug)
	COMMON_FLAGS += -Og
	COMMON_FLAGS += -g3 -ggdb
endif

### C flags

# Macro Defines (without -D prefix):
C_DEFS = \
USE_HAL_DRIVER \
STM32F303xC

CFLAGS += $(COMMON_FLAGS)
CFLAGS += $(addprefix -D,$(C_DEFS))
CFLAGS += $(addprefix -I,$(C_INC_DIRS))
CFLAGS += -Wall -Wextra -Wpedantic
CFLAGS += -fdata-sections -ffunction-sections -fstack-usage

# Add DEBUG define for debug builds
ifeq ($(BUILD),debug)
	CFLAGS += -DDEBUG
endif


# Flags for dependency generation
CFLAGS +=-MMD -MP -MF $(DEP_DIR)/$*.d

### ASM flags
ASM_DEFS = 

ASMFLAGS += $(COMMON_FLAGS)
ASMFLAGS += $(addprefix -D,$(ASM_DEFS))
ASMFLAGS += $(addprefix -I,$(ASM_INC_DIRS))

### Linker flags

# Linker Script:
LDSCRIPT = STM32F303RCTx_FLASH.ld

LIBS = -lc -lm -lnosys
LIBDIR = 

LDFLAGS = $(COMMON_FLAGS) -T$(LDSCRIPT) $(LIBDIR) $(LIBS) -Wl,-Map=$(BUILD_DIR)/$(TARGET).map,--cref -Wl,--gc-sections 


############################################
### Makefile variable generation & Logic ###
############################################

# File discovery/list generation:
C_SOURCES += $(foreach dir, $(C_DIRS), $(wildcard $(dir)/*.c))
ASM_SOURCES += $(foreach dir, $(ASM_DIRS), $(wildcard $(dir)/*.s))

OBJECTS += $(addprefix $(BUILD_DIR)/, $(C_SOURCES:.c=.o))
OBJECTS += $(addprefix $(BUILD_DIR)/, $(ASM_SOURCES:.s=.o))

# Make sure the selected buildmode actually exists:
ifneq ($(BUILD),release)
ifneq ($(BUILD),debug)
$(error Build-config BUILD=$(BUILD) unknown.)
endif
endif

########################
###  Makefile Rules  ###
########################

.PHONY: all clean info

all: $(BUILD_DIR)/$(TARGET).elf $(BUILD_DIR)/$(TARGET).hex $(BUILD_DIR)/$(TARGET).bin

# Link ELF target
$(BUILD_DIR)/$(TARGET).elf: $(OBJECTS) makefile 
	@echo "Linking $@..."
	@mkdir -p $(dir $@)
	@$(CC) $(OBJECTS) $(LDFLAGS) -o $@
	@$(SZ) $@ 
	@echo "Successfully built $@"


# Compile C files
$(BUILD_DIR)/%.o: %.c makefile 
	@echo "Compiling $<..."
	@mkdir -p $(dir $@) 
	@mkdir -p $(dir $(DEP_DIR)/$*.d)
	@$(CC) -c $(CFLAGS) -Wa,-a,-ad,-alms=$(BUILD_DIR)/$(<:.c=.lst) $< -o $@

# Compile ASM files
$(BUILD_DIR)/%.o: %.s makefile
	@echo "Compiling $<..."
	@mkdir -p $(dir $@) 
	@mkdir -p $(dir $(DEP_DIR)/$*.d)
	@$(CC) -c $(CFLAGS) -Wa,-a,-ad,-alms=$(BUILD_DIR)/$(<:.c=.lst) $< -o $@

# Generate HEX file
$(BUILD_DIR)/$(TARGET).hex: $(BUILD_DIR)/$(TARGET).elf makefile | $(BUILD_DIR)
	@mkdir -p $(dir $@)
	@$(HEX) $< $@
	@echo "Successfully built $@"

# Generate BIN file
$(BUILD_DIR)/$(TARGET).bin: $(BUILD_DIR)/$(TARGET).elf makefile | $(BUILD_DIR)
	@mkdir -p $(dir $@)
	@$(BIN) $< $@
	@echo "Successfully built $@"

clean: 
	-rm -rf $(BUILD_DIR)
	-rm -rf $(DEP_DIR)

info:
	@echo $(C_SOURCES)
	@echo $(ASM_SOURCES)


ifneq ($(MAKECMDGOALS),clean) # Prevent regeneration of .d files on non-build targets:
ifneq ($(MAKECMDGOALS),info) 
include $(wildcard $(DEP_DIR)/*.d) # Include generated dependency files, to make .o files depend on the right .h files
endif
endif 
