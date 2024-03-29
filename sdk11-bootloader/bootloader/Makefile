PROJECT_NAME := sdk11_bootloader

export OUTPUT_FILENAME
#MAKEFILE_NAME := $(CURDIR)/$(word $(words $(MAKEFILE_LIST)),$(MAKEFILE_LIST))
MAKEFILE_NAME := $(MAKEFILE_LIST)
MAKEFILE_DIR := $(dir $(MAKEFILE_NAME) ) 

NRF_SDK_PATH = /mnt/c/dev/nRF5_SDK_11.0.0_89a8197
TEMPLATE_PATH = $(NRF_SDK_PATH)/components/toolchain/gcc
ifeq ($(OS),Windows_NT)
include $(TEMPLATE_PATH)/Makefile.windows
else
include $(TEMPLATE_PATH)/Makefile.posix
endif

MK := mkdir
RM := rm -rf

#echo suspend
ifeq ("$(VERBOSE)","1")
NO_ECHO := 
else
NO_ECHO := @
endif

# Only assigned if not already defined
CFG_BOOTLOADER_ADDRESS ?= 7A000


# Toolchain commands
CC              := '$(GNU_INSTALL_ROOT)/bin/$(GNU_PREFIX)-gcc'
AS              := '$(GNU_INSTALL_ROOT)/bin/$(GNU_PREFIX)-as'
AR              := '$(GNU_INSTALL_ROOT)/bin/$(GNU_PREFIX)-ar' -r
LD              := '$(GNU_INSTALL_ROOT)/bin/$(GNU_PREFIX)-ld'
NM              := '$(GNU_INSTALL_ROOT)/bin/$(GNU_PREFIX)-nm'
OBJDUMP         := '$(GNU_INSTALL_ROOT)/bin/$(GNU_PREFIX)-objdump'
OBJCOPY         := '$(GNU_INSTALL_ROOT)/bin/$(GNU_PREFIX)-objcopy'
SIZE            := '$(GNU_INSTALL_ROOT)/bin/$(GNU_PREFIX)-size'

#function for removing duplicates in a list
remduplicates = $(strip $(if $1,$(firstword $1) $(call remduplicates,$(filter-out $(firstword $1),$1))))

#source common to all targets
C_SOURCE_FILES += \
$(abspath $(NRF_SDK_PATH)/components/libraries/util/app_error.c) \
$(abspath $(NRF_SDK_PATH)/components/libraries/util/app_error_weak.c) \
$(abspath $(NRF_SDK_PATH)/components/libraries/scheduler/app_scheduler.c) \
$(abspath $(NRF_SDK_PATH)/components/libraries/timer/app_timer.c) \
$(abspath $(NRF_SDK_PATH)/components/libraries/timer/app_timer_appsh.c) \
$(abspath $(NRF_SDK_PATH)/components/libraries/util/app_util_platform.c) \
$(abspath ../bootloader_dfu/bootloader.c) \
$(abspath ../bootloader_dfu/bootloader_settings.c) \
$(abspath ../bootloader_dfu/bootloader_util.c) \
$(abspath $(NRF_SDK_PATH)/components/libraries/crc16/crc16.c) \
$(abspath ../bootloader_dfu/dfu_single_bank.c) \
$(abspath ../bootloader_dfu/dfu_init_template.c) \
$(abspath ../bootloader_dfu/dfu_transport_ble.c) \
$(abspath $(NRF_SDK_PATH)/components/libraries/hci/hci_mem_pool.c) \
$(abspath $(NRF_SDK_PATH)/components/libraries/util/nrf_assert.c) \
$(abspath $(NRF_SDK_PATH)/components/drivers_nrf/delay/nrf_delay.c) \
$(abspath $(NRF_SDK_PATH)/components/drivers_nrf/common/nrf_drv_common.c) \
$(abspath $(NRF_SDK_PATH)/components/drivers_nrf/pstorage/pstorage_raw.c) \
$(abspath dfu_ble_svc.c) \
$(abspath main.c) \
$(abspath $(NRF_SDK_PATH)/components/ble/common/ble_advdata.c) \
$(abspath $(NRF_SDK_PATH)/components/ble/common/ble_conn_params.c) \
$(abspath $(NRF_SDK_PATH)/components/ble/ble_services/ble_dfu/ble_dfu.c) \
$(abspath $(NRF_SDK_PATH)/components/ble/common/ble_srv_common.c) \
$(abspath $(NRF_SDK_PATH)/components/toolchain/system_nrf52.c) \
$(abspath $(NRF_SDK_PATH)/components/softdevice/common/softdevice_handler/softdevice_handler.c) \
$(abspath $(NRF_SDK_PATH)/components/softdevice/common/softdevice_handler/softdevice_handler_appsh.c) \

#assembly files common to all targets
ASM_SOURCE_FILES  = $(abspath $(NRF_SDK_PATH)/components/toolchain/gcc/gcc_startup_nrf52.s)

#includes common to all targets
INC_PATHS  = -I$(abspath config)
INC_PATHS += -I$(abspath ./)
INC_PATHS += -I$(abspath ../bootloader_dfu)
INC_PATHS += -I$(abspath $(NRF_SDK_PATH)/components/libraries/scheduler)
INC_PATHS += -I$(abspath $(NRF_SDK_PATH)/components/drivers_nrf/config)
INC_PATHS += -I$(abspath $(NRF_SDK_PATH)/examples/bsp)
INC_PATHS += -I$(abspath $(NRF_SDK_PATH)/components/drivers_nrf/delay)
INC_PATHS += -I$(abspath $(NRF_SDK_PATH)/components/libraries/crc16)
INC_PATHS += -I$(abspath $(NRF_SDK_PATH)/components/softdevice/s132/headers/nrf52)
INC_PATHS += -I$(abspath $(NRF_SDK_PATH)/components/libraries/util)
INC_PATHS += -I$(abspath $(NRF_SDK_PATH)/components/ble/common)
INC_PATHS += -I$(abspath $(NRF_SDK_PATH)/components/drivers_nrf/pstorage)
INC_PATHS += -I$(abspath ../bootloader_dfu/ble_transport)
INC_PATHS += -I$(abspath $(NRF_SDK_PATH)/components/device)
INC_PATHS += -I$(abspath $(NRF_SDK_PATH)/components/libraries/hci)
INC_PATHS += -I$(abspath $(NRF_SDK_PATH)/components/libraries/timer)
INC_PATHS += -I$(abspath $(NRF_SDK_PATH)/components/softdevice/s132/headers)
INC_PATHS += -I$(abspath $(NRF_SDK_PATH)/components/toolchain/CMSIS/Include)
INC_PATHS += -I$(abspath $(NRF_SDK_PATH)/components/drivers_nrf/hal)
INC_PATHS += -I$(abspath $(NRF_SDK_PATH)/components/toolchain/gcc)
INC_PATHS += -I$(abspath $(NRF_SDK_PATH)/components/toolchain)
INC_PATHS += -I$(abspath $(NRF_SDK_PATH)/components/drivers_nrf/common)
INC_PATHS += -I$(abspath $(NRF_SDK_PATH)/components/softdevice/common/softdevice_handler)
INC_PATHS += -I$(abspath $(NRF_SDK_PATH)/components/ble/ble_services/ble_dfu)

OBJECT_DIRECTORY = _build
LISTING_DIRECTORY = $(OBJECT_DIRECTORY)
OUTPUT_BINARY_DIRECTORY = _bin

# Sorting removes duplicates
BUILD_DIRECTORIES := $(sort $(OBJECT_DIRECTORY) $(OUTPUT_BINARY_DIRECTORY) $(LISTING_DIRECTORY) )
BUILD_TEMP_DIRECTORIES := $(sort $(OBJECT_DIRECTORY) $(LISTING_DIRECTORY) )

#flags common to all targets
CFLAGS  = -DNRF52
CFLAGS += -DBOARD_CUSTOM
CFLAGS += -DCFG_BOOTLOADER_ADDRESS=0x$(CFG_BOOTLOADER_ADDRESS)
CFLAGS += -DNRF52_PAN_12
CFLAGS += -DNRF52_PAN_15
CFLAGS += -DNRF52_PAN_58
CFLAGS += -DNRF52_PAN_55
CFLAGS += -DNRF52_PAN_54
CFLAGS += -DNRF52_PAN_31
CFLAGS += -DNRF52_PAN_30
CFLAGS += -DNRF52_PAN_51
CFLAGS += -DNRF52_PAN_36
CFLAGS += -DNRF52_PAN_53
CFLAGS += -D__HEAP_SIZE=0
CFLAGS += -DS132
CFLAGS += -DCONFIG_GPIO_AS_PINRESET
CFLAGS += -DBLE_STACK_SUPPORT_REQD
CFLAGS += -DBSP_DEFINES_ONLY
CFLAGS += -DSWI_DISABLE0
CFLAGS += -DNRF52_PAN_20
CFLAGS += -DNRF52_PAN_64
CFLAGS += -DSOFTDEVICE_PRESENT
CFLAGS += -DNRF52_PAN_62
CFLAGS += -DNRF52_PAN_63
CFLAGS += -mcpu=cortex-m4
CFLAGS += -mthumb -mabi=aapcs --std=gnu99
CFLAGS += -Wall -Werror -Os -g3
CFLAGS += -mfloat-abi=hard -mfpu=fpv4-sp-d16
# keep every function in separate section. This will allow linker to dump unused functions
CFLAGS += -ffunction-sections -fdata-sections -fno-strict-aliasing
CFLAGS += -fno-builtin --short-enums 
#CFLAGS += -flto
# keep every function in separate section. This will allow linker to dump unused functions
LDFLAGS += -Xlinker -Map=$(LISTING_DIRECTORY)/$(OUTPUT_FILENAME).map
LDFLAGS += -mthumb -mabi=aapcs -L $(TEMPLATE_PATH) -T$(LINKER_SCRIPT)
LDFLAGS += -mcpu=cortex-m4
LDFLAGS += -mfloat-abi=hard -mfpu=fpv4-sp-d16
# let linker to dump unused sections
LDFLAGS += -Wl,--gc-sections
#LDFLAGS += -flto
# use newlib in nano version
LDFLAGS += --specs=nano.specs -lc -lnosys

# Assembler flags
ASMFLAGS += -x assembler-with-cpp
ASMFLAGS += -DNRF52
ASMFLAGS += -DBOARD_CUSTOM
ASMFLAGS += -DCFG_BOOTLOADER_ADDRESS=0x$(CFG_BOOTLOADER_ADDRESS)
ASMFLAGS += -DNRF52_PAN_12
ASMFLAGS += -DNRF52_PAN_15
ASMFLAGS += -DNRF52_PAN_58
ASMFLAGS += -DNRF52_PAN_55
ASMFLAGS += -DNRF52_PAN_54
ASMFLAGS += -DNRF52_PAN_31
ASMFLAGS += -DNRF52_PAN_30
ASMFLAGS += -DNRF52_PAN_51
ASMFLAGS += -DNRF52_PAN_36
ASMFLAGS += -DNRF52_PAN_53
ASMFLAGS += -D__HEAP_SIZE=0
ASMFLAGS += -DS132
ASMFLAGS += -DCONFIG_GPIO_AS_PINRESET
ASMFLAGS += -DBLE_STACK_SUPPORT_REQD
ASMFLAGS += -DBSP_DEFINES_ONLY
ASMFLAGS += -DSWI_DISABLE0
ASMFLAGS += -DNRF52_PAN_20
ASMFLAGS += -DNRF52_PAN_64
ASMFLAGS += -DSOFTDEVICE_PRESENT
ASMFLAGS += -DNRF52_PAN_62
ASMFLAGS += -DNRF52_PAN_63

#default target - first one defined
default: clean $(PROJECT_NAME)

#building all targets
all: clean
	$(NO_ECHO)$(MAKE) -f $(MAKEFILE_NAME) -C $(MAKEFILE_DIR) -e cleanobj
	$(NO_ECHO)$(MAKE) -f $(MAKEFILE_NAME) -C $(MAKEFILE_DIR) -e $(PROJECT_NAME)

#target for printing all targets
help:
	@echo following targets are available:
	@echo 	$(PROJECT_NAME)

C_SOURCE_FILE_NAMES = $(notdir $(C_SOURCE_FILES))
C_PATHS = $(call remduplicates, $(dir $(C_SOURCE_FILES) ) )
C_OBJECTS = $(addprefix $(OBJECT_DIRECTORY)/, $(C_SOURCE_FILE_NAMES:.c=.o) )

ASM_SOURCE_FILE_NAMES = $(notdir $(ASM_SOURCE_FILES))
ASM_PATHS = $(call remduplicates, $(dir $(ASM_SOURCE_FILES) ))
ASM_OBJECTS = $(addprefix $(OBJECT_DIRECTORY)/, $(ASM_SOURCE_FILE_NAMES:.s=.o) )

vpath %.c $(C_PATHS)
vpath %.s $(ASM_PATHS)

OBJECTS = $(C_OBJECTS) $(ASM_OBJECTS)

$(PROJECT_NAME): OUTPUT_FILENAME := $(PROJECT_NAME)-$(CFG_BOOTLOADER_ADDRESS)
$(PROJECT_NAME): LINKER_SCRIPT=dfu_gcc_nrf52-$(CFG_BOOTLOADER_ADDRESS).ld

$(PROJECT_NAME): $(BUILD_DIRECTORIES) $(OBJECTS)
	@echo Linking target: $(OUTPUT_FILENAME).out
	$(NO_ECHO)$(CC) $(LDFLAGS) $(OBJECTS) $(LIBS) -lm -o $(OUTPUT_BINARY_DIRECTORY)/$(OUTPUT_FILENAME).out
	$(NO_ECHO)$(MAKE) -f $(MAKEFILE_NAME) -C $(MAKEFILE_DIR) -e finalize

## Create build directories
$(BUILD_DIRECTORIES):
	echo $(MAKEFILE_NAME)
	$(MK) $@

# Create objects from C SRC files
$(OBJECT_DIRECTORY)/%.o: %.c
	@echo Compiling file: $(notdir $<)
	$(NO_ECHO)$(CC) $(CFLAGS) $(INC_PATHS) -c -o $@ $<

# Assemble files
$(OBJECT_DIRECTORY)/%.o: %.s
	@echo Assembly file: $(notdir $<)
	$(NO_ECHO)$(CC) $(ASMFLAGS) $(INC_PATHS) -c -o $@ $<
# Link
$(OUTPUT_BINARY_DIRECTORY)/$(OUTPUT_FILENAME).out: $(BUILD_DIRECTORIES) $(OBJECTS)
	@echo Linking target: $(OUTPUT_FILENAME).out
	$(NO_ECHO)$(CC) $(LDFLAGS) $(OBJECTS) $(LIBS) -lm -o $(OUTPUT_BINARY_DIRECTORY)/$(OUTPUT_FILENAME).out
## Create binary .bin file from the .out file
$(OUTPUT_BINARY_DIRECTORY)/$(OUTPUT_FILENAME).bin: $(OUTPUT_BINARY_DIRECTORY)/$(OUTPUT_FILENAME).out
	@echo Preparing: $(OUTPUT_FILENAME).bin
	$(NO_ECHO)$(OBJCOPY) -O binary $(OUTPUT_BINARY_DIRECTORY)/$(OUTPUT_FILENAME).out $(OUTPUT_BINARY_DIRECTORY)/$(OUTPUT_FILENAME).bin

## Create binary .hex file from the .out file
$(OUTPUT_BINARY_DIRECTORY)/$(OUTPUT_FILENAME).hex: $(OUTPUT_BINARY_DIRECTORY)/$(OUTPUT_FILENAME).out
	@echo Preparing: $(OUTPUT_FILENAME).hex
	$(NO_ECHO)$(OBJCOPY) -O ihex $(OUTPUT_BINARY_DIRECTORY)/$(OUTPUT_FILENAME).out $(OUTPUT_BINARY_DIRECTORY)/$(OUTPUT_FILENAME).hex

finalize: genbin genhex echosize

genbin:
	@echo Preparing: $(OUTPUT_FILENAME).bin
	$(NO_ECHO)$(OBJCOPY) -O binary $(OUTPUT_BINARY_DIRECTORY)/$(OUTPUT_FILENAME).out $(OUTPUT_BINARY_DIRECTORY)/$(OUTPUT_FILENAME).bin

## Create binary .hex file from the .out file
genhex: 
	@echo Preparing: $(OUTPUT_FILENAME).hex
	$(NO_ECHO)$(OBJCOPY) -O ihex $(OUTPUT_BINARY_DIRECTORY)/$(OUTPUT_FILENAME).out $(OUTPUT_BINARY_DIRECTORY)/$(OUTPUT_FILENAME).hex
echosize:
	-@echo ''
	$(NO_ECHO)$(SIZE) $(OUTPUT_BINARY_DIRECTORY)/$(OUTPUT_FILENAME).out
	-@echo ''

clean:
	$(RM) $(BUILD_TEMP_DIRECTORIES)

cleanobj:
	$(RM) $(BUILD_TEMP_DIRECTORIES)/*.o
flash: $(PROJECT_NAME)
	@echo Flashing: $(OUTPUT_BINARY_DIRECTORY)/$<.hex
	nrfjprog --program $(OUTPUT_BINARY_DIRECTORY)/$<.hex -f nrf52  --chiperase
	nrfjprog --reset -f nrf52

## Flash softdevice
