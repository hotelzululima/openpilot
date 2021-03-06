 #####
 # Project: OpenPilot
 #    
 #  
 # Makefile PX2 FMU bootloader.
 #
 # The OpenPilot Team, http://www.openpilot.org, Copyright (C) 2009.
 #    
 # 
 # This program is free software; you can redistribute it and/or modify 
 # it under the terms of the GNU General Public License as published by 
 # the Free Software Foundation; either version 3 of the License, or 
 # (at your option) any later version.
 # 
 # This program is distributed in the hope that it will be useful, but 
 # WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY 
 # or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General Public License 
 # for more details.
 # 
 # You should have received a copy of the GNU General Public License along 
 # with this program; if not, write to the Free Software Foundation, Inc., 
 # 59 Temple Place, Suite 330, Boston, MA 02111-1307 USA
 #####

WHEREAMI := $(dir $(lastword $(MAKEFILE_LIST)))
TOP      := $(realpath $(WHEREAMI)/../../../)
include $(TOP)/make/firmware-defs.mk

# Set developer code and compile options
# Set to YES to compile for debugging
DEBUG ?= YES

# Set to YES to use the Servo output pins for debugging via scope or logic analyser
ENABLE_DEBUG_PINS ?= NO

# Set to Yes to enable the AUX UART which is mapped on the S1 (Tx) and S2 (Rx) servo outputs  
ENABLE_AUX_UART ?= NO

# We ARE the bootloader
OVERRIDE USE_BOOTLOADER = NO

# Set to YES when using Code Sourcery toolchain
CODE_SOURCERY ?= NO

# Remove command is different for Code Sourcery on Windows
ifeq ($(CODE_SOURCERY), YES)
REMOVE_CMD = cs-rm
else
REMOVE_CMD = rm
endif

FLASH_TOOL = OPENOCD

# MCU name, submodel and board
# - MCU used for compiler-option (-mcpu)
# - MODEL used for linker-script name (-T) and passed as define
# - BOARD just passed as define (optional)
MCU      = cortex-m3
CHIP     = STM32F205RGT
BOARD    = STM32205_PX2FMU_Rev1
OSCILLATOR_FREQ	= 8000000

# Directory for output files (lst, obj, dep, elf, sym, map, hex, bin etc.)
OUTDIR = $(TOP)/build/bl_px2_fmu

# Target file name (without extension).
TARGET = PX2_FMU_BL

# Paths
OPSYSTEM = .
OPSYSTEMINC = $(OPSYSTEM)/inc
OPUAVTALK = ./UAVTalk
OPUAVTALKINC = $(OPUAVTALK)/inc
OPUAVOBJ = ./UAVObjects
OPUAVOBJINC = $(OPUAVOBJ)/inc
OPTESTS  = ./Tests
OPMODULEDIR = ./Modules
FLIGHTLIB = ../../Libraries
FLIGHTLIBINC = ../../Libraries/inc
PIOS = ../../PiOS
PIOSINC = $(PIOS)/inc
PIOSSTM32F2XX = $(PIOS)/STM32F2xx
PIOSCOMMON = $(PIOS)/Common
PIOSBOARDS = $(PIOS)/Boards
APPLIBDIR = $(PIOSSTM32F2XX)/Libraries
STMLIBDIR = $(APPLIBDIR)
STMSPDDIR = $(STMLIBDIR)/STM32F2xx_StdPeriph_Driver
STMUSBDEVDIR = $(STMLIBDIR)/STM32_USB_Device_Library
STMUSBCOREDIR = $(STMLIBDIR)/STM32_USB_OTG_Driver
STMSPDSRCDIR = $(STMSPDDIR)/src
STMSPDINCDIR = $(STMSPDDIR)/inc
CMSISDIR  = $(STMLIBDIR)/CMSIS/Core/CM3
DOSFSDIR  = $(APPLIBDIR)/dosfs
MSDDIR  = $(APPLIBDIR)/msd
RTOSDIR = $(APPLIBDIR)/FreeRTOS
RTOSSRCDIR = $(RTOSDIR)/Source
RTOSINCDIR = $(RTOSSRCDIR)/include
DOXYGENDIR = ../Doc/Doxygen

# List C source files here. (C dependencies are automatically generated.)
# use file-extension c for "c-only"-files

## OPENPILOT_BL CORE:
SRC += $(OPSYSTEM)/main.c
SRC += $(OPSYSTEM)/pios_board.c
SRC += $(OPSYSTEM)/op_dfu.c
SRC += $(OPSYSTEM)/stopwatch.c
SRC += $(OPSYSTEM)/ssp.c
SRC += $(OPSYSTEM)/dcc_stdio.c
SRC += $(OPSYSTEM)/cm3_fault_handlers.c

## PIOS Hardware (STM32F2xx)
# XXX  should just link with a .a
SRC += $(PIOSSTM32F2XX)/pios_sys.c
SRC += $(PIOSSTM32F2XX)/pios_led.c
SRC += $(PIOSSTM32F2XX)/pios_delay.c
SRC += $(PIOSSTM32F2XX)/pios_usart.c
SRC += $(PIOSSTM32F2XX)/pios_irq.c
SRC += $(PIOSSTM32F2XX)/pios_spi.c
SRC += $(PIOSSTM32F2XX)/pios_debug.c
SRC += $(PIOSSTM32F2XX)/pios_gpio.c

# PIOS USB related files (seperated to make code maintenance more easy)
# XXX  should just link with a .a
SRC	+=	$(PIOSSTM32F2XX)/pios_usb_bsp.c
SRC	+=	$(PIOSSTM32F2XX)/pios_usb_hid_core.c
SRC	+=	$(PIOSSTM32F2XX)/pios_usb_hid_desc.c
SRC	+=	$(PIOSSTM32F2XX)/pios_usb_hid.c
SRC	+=	$(PIOSSTM32F2XX)/pios_usb_usr.c

## PIOS Hardware (Common)
# XXX  should just link with a .a
SRC += $(PIOSCOMMON)/pios_com.c
SRC += $(PIOSCOMMON)/pios_opahrs_v0.c
SRC += $(PIOSCOMMON)/pios_bl_helper.c
SRC += $(PIOSCOMMON)/pios_iap.c
SRC += $(PIOSCOMMON)/pios_opahrs_proto.c
SRC += $(PIOSCOMMON)/printf-stdarg.c

## Libraries for flight calculations
# XXX should just link with a .a
SRC += $(FLIGHTLIB)/fifo_buffer.c

## CMSIS for STM32
# XXX  should just link with a .a
SRC += $(CMSISDIR)/core_cm3.c
SRC += $(CMSISDIR)/system_stm32f2xx.c

## Used parts of the STM-Library
# XXX  should just link with a .a
SRC += $(STMSPDSRCDIR)/stm32f2xx_crc.c
SRC += $(STMSPDSRCDIR)/stm32f2xx_exti.c
SRC += $(STMSPDSRCDIR)/stm32f2xx_flash.c
SRC += $(STMSPDSRCDIR)/stm32f2xx_gpio.c
SRC += $(STMSPDSRCDIR)/stm32f2xx_pwr.c
SRC += $(STMSPDSRCDIR)/stm32f2xx_rcc.c
SRC += $(STMSPDSRCDIR)/stm32f2xx_rtc.c
SRC += $(STMSPDSRCDIR)/stm32f2xx_spi.c
SRC += $(STMSPDSRCDIR)/stm32f2xx_tim.c
SRC += $(STMSPDSRCDIR)/stm32f2xx_usart.c
SRC += $(STMSPDSRCDIR)/stm32f2xx_dbgmcu.c
SRC += $(STMSPDSRCDIR)/misc.c

## STM32 USB Library
# XXX  should just link with a .a
SRC	+= $(STMUSBDEVDIR)/Core/src/usbd_core.c
SRC	+= $(STMUSBDEVDIR)/Core/src/usbd_desc.c
SRC	+= $(STMUSBDEVDIR)/Core/src/usbd_ioreq.c
SRC	+= $(STMUSBDEVDIR)/Core/src/usbd_req.c
SRC += $(STMUSBCOREDIR)/src/usb_core.c
SRC += $(STMUSBCOREDIR)/src/usb_dcd_int.c
SRC += $(STMUSBCOREDIR)/src/usb_dcd.c

# List C source files here which must be compiled in ARM-Mode (no -mthumb).
# use file-extension c for "c-only"-files
## just for testing, timer.c could be compiled in thumb-mode too
SRCARM = 

# List C++ source files here.
# use file-extension .cpp for C++-files (not .C)
CPPSRC = 

# List C++ source files here which must be compiled in ARM-Mode.
# use file-extension .cpp for C++-files (not .C)
#CPPSRCARM = $(TARGET).cpp
CPPSRCARM = 

# List Assembler source files here.
# Make them always end in a capital .S. Files ending in a lowercase .s
# will not be considered source files but generated files (assembler
# output from the compiler), and will be deleted upon "make clean"!
# Even though the DOS/Win* filesystem matches both .s and .S the same,
# it will preserve the spelling of the filenames, and gcc itself does
# care about how the name is spelled on its command-line.
ASRC = $(PIOSSTM32F2XX)/startup_stm32f2xx.S

# List Assembler source files here which must be assembled in ARM-Mode..
ASRCARM = 

# List any extra directories to look for include files here.
#    Each directory must be seperated by a space.
EXTRAINCDIRS  =  $(OPSYSTEM)
EXTRAINCDIRS  += $(OPSYSTEMINC)
EXTRAINCDIRS  += $(OPUAVTALK)
EXTRAINCDIRS  += $(OPUAVTALKINC)
EXTRAINCDIRS  += $(OPUAVOBJ)
EXTRAINCDIRS  += $(OPUAVOBJINC)
EXTRAINCDIRS  += $(PIOS)
EXTRAINCDIRS  += $(PIOSSTM32F2XX)/inc		// must before PIOSINC until usb_conf.h moves
EXTRAINCDIRS  += $(PIOSINC)
EXTRAINCDIRS  += $(FLIGHTLIBINC)
EXTRAINCDIRS  += $(PIOSCOMMON)
EXTRAINCDIRS  += $(PIOSBOARDS)
EXTRAINCDIRS  += $(STMSPDINCDIR)
EXTRAINCDIRS  += $(STMUSBDEVDIR)/Core/inc
EXTRAINCDIRS  += $(STMUSBCOREDIR)/inc
EXTRAINCDIRS  += $(CMSISDIR)
EXTRAINCDIRS  += $(DOSFSDIR)
EXTRAINCDIRS  += $(MSDDIR)
EXTRAINCDIRS  += $(RTOSINCDIR)
EXTRAINCDIRS  += $(APPLIBDIR)
EXTRAINCDIRS  += $(RTOSSRCDIR)/portable/GCC/ARM_CM3



# List any extra directories to look for library files here.
# Also add directories where the linker should search for
# includes from linker-script to the list
#     Each directory must be seperated by a space.
EXTRA_LIBDIRS = 

# Extra Libraries
#    Each library-name must be seperated by a space.
#    i.e. to link with libxyz.a, libabc.a and libefsl.a: 
#    EXTRA_LIBS = xyz abc efsl
# for newlib-lpc (file: libnewlibc-lpc.a):
#    EXTRA_LIBS = newlib-lpc
EXTRA_LIBS =

# Path to Linker-Scripts
LINKERSCRIPTPATH = $(PIOSSTM32F2XX)

# Optimization level, can be [0, 1, 2, 3, s]. 
# 0 = turn off optimization. s = optimize for size.
# (Note: 3 is not always the best optimization level. See avr-libc FAQ.)

ifeq ($(DEBUG),YES)
OPT = 0
else
OPT = s
endif

# Output format. (can be ihex or binary or both)
#  binary to create a load-image in raw-binary format i.e. for SAM-BA, 
#  ihex to create a load-image in Intel hex format
#LOADFORMAT = ihex
#LOADFORMAT = binary
LOADFORMAT = both

# Debugging format.
DEBUGF = dwarf-2

# Place project-specific -D (define) and/or 
# -U options for C here.
CDEFS = -DSTM32F2XX
CDEFS += -DHSE_VALUE=$(OSCILLATOR_FREQ)
CDEFS += -DUSE_STDPERIPH_DRIVER
CDEFS += -DUSE_$(BOARD)
ifeq ($(ENABLE_DEBUG_PINS), YES)
CDEFS += -DPIOS_ENABLE_DEBUG_PINS
endif
ifeq ($(ENABLE_AUX_UART), YES)
CDEFS += -DPIOS_ENABLE_AUX_UART
endif
ifeq ($(USE_BOOTLOADER), YES)
CDEFS += -DUSE_BOOTLOADER
endif


# Place project-specific -D and/or -U options for 
# Assembler with preprocessor here.
#ADEFS = -DUSE_IRQ_ASM_WRAPPER
ADEFS = -D__ASSEMBLY__

# Compiler flag to set the C Standard level.
# c89   - "ANSI" C
# gnu89 - c89 plus GCC extensions
# c99   - ISO C99 standard (not yet fully implemented)
# gnu99 - c99 plus GCC extensions
CSTANDARD = -std=gnu99

#-----

# Compiler flags.

#  -g*:          generate debugging information
#  -O*:          optimization level
#  -f...:        tuning, see GCC manual and avr-libc documentation
#  -Wall...:     warning level
#  -Wa,...:      tell GCC to pass this to the assembler.
#    -adhlns...: create assembler listing
#
# Flags for C and C++ (arm-elf-gcc/arm-elf-g++)

ifeq ($(DEBUG),YES)
CFLAGS = -g$(DEBUGF) -DDEBUG
endif

CFLAGS += -O$(OPT)

# if we leave this out then -gc_sections will strip the startup code... bad 
#ifeq ($(DEBUG),NO)
CFLAGS += -ffunction-sections
#endif

CFLAGS += -mcpu=$(MCU)
CFLAGS += $(CDEFS)
CFLAGS += $(patsubst %,-I%,$(EXTRAINCDIRS)) -I.

CFLAGS += -mapcs-frame 
CFLAGS += -fomit-frame-pointer
CFLAGS += -msoft-float
#CFLAGS += -fpromote-loop-indices

CFLAGS += -Wall 
CFLAGS += -Werror
CFLAGS += -Wa,-adhlns=$(addprefix $(OUTDIR)/, $(notdir $(addsuffix .lst, $(basename $<))))
# Compiler flags to generate dependency files:
CFLAGS += -MD -MP -MF $(OUTDIR)/dep/$(@F).d

# flags only for C
#CONLYFLAGS += -Wnested-externs 
CONLYFLAGS += $(CSTANDARD)

# Assembler flags.
#  -Wa,...:    tell GCC to pass this to the assembler.
#  -ahlns:     create listing
ASFLAGS  = -mcpu=$(MCU) -I. -x assembler-with-cpp
ASFLAGS += $(ADEFS)
ASFLAGS += -Wa,-adhlns=$(addprefix $(OUTDIR)/, $(notdir $(addsuffix .lst, $(basename $<))))
ASFLAGS += $(patsubst %,-I%,$(EXTRAINCDIRS))

MATH_LIB = -lm

# Linker flags.
#  -Wl,...:     tell GCC to pass this to linker.
#    -Map:      create map file
#    --cref:    add cross reference to  map file
LDFLAGS = -nostartfiles -Wl,-Map=$(OUTDIR)/$(TARGET).map,--cref,--gc-sections
ifeq ($(DEBUG),NO)
LDFLAGS += -Wl,-static -Wl,-s
endif
LDFLAGS += $(patsubst %,-L%,$(EXTRA_LIBDIRS))
LDFLAGS += -lc
LDFLAGS += $(patsubst %,-l%,$(EXTRA_LIBS))
LDFLAGS += -msoft-float
LDFLAGS += $(MATH_LIB)
LDFLAGS += -lc -lgcc

# Set linker-script name
LDFLAGS +=-T$(LINKERSCRIPTPATH)/link_STM32F2xx_bootloader.ld -L$(LINKERSCRIPTPATH)

# ---------------------------------------------------------------------------
# Options for OpenOCD flash-programming
# see openocd.pdf/openocd.texi for further information
#
OOCD_LOADFILE+=$(OUTDIR)/$(TARGET).elf
# if OpenOCD is in the $PATH just set OOCD_EXE=openocd
OOCD_EXE=openocd
# debug level
OOCD_CL=-d0
# interface and board/target settings (using the OOCD target-library here)
UNAME := $(shell uname)
ifeq ($(UNAME), Darwin)
  OOCD_CL+=-f ../../Project/OpenOCD/floss-jtag.openpilot.osx.cfg -f ../../Project/OpenOCD/stm32.cfg
else
  OOCD_CL+=-f ../../Project/OpenOCD/floss-jtag.openpilot.cfg -f ../../Project/OpenOCD/stm32.cfg
endif
# initialize
OOCD_CL+=-c init
# show the targets
OOCD_CL+=-c targets
# commands to prepare flash-write
OOCD_CL+= -c "reset halt"
# flash erase 
OOCD_CL+=-c "stm32x mass_erase 0"
# flash-write
OOCD_CL+=-c "flash write_image $(OOCD_LOADFILE)" 
# Verify
OOCD_CL+=-c "verify_image $(OOCD_LOADFILE)"
# reset target
OOCD_CL+=-c "reset run"
# terminate OOCD after programming
OOCD_CL+=-c shutdown
# ---------------------------------------------------------------------------


# Define programs and commands.
REMOVE  = $(REMOVE_CMD) -f
###SHELL   = sh
###COPY    = cp

# List of all source files.
ALLSRC     = $(ASRCARM) $(ASRC) $(SRCARM) $(SRC) $(CPPSRCARM) $(CPPSRC)
# List of all source files without directory and file-extension.
ALLSRCBASE = $(notdir $(basename $(ALLSRC)))

# Define all object files.
ALLOBJ     = $(addprefix $(OUTDIR)/, $(addsuffix .o, $(ALLSRCBASE)))

# Define all listing files (used for make clean).
LSTFILES   = $(addprefix $(OUTDIR)/, $(addsuffix .lst, $(ALLSRCBASE)))
# Define all depedency-files (used for make clean).
DEPFILES   = $(addprefix $(OUTDIR)/dep/, $(addsuffix .o.d, $(ALLSRCBASE)))

# Default target.
all: build

ifeq ($(LOADFORMAT),ihex)
build: elf hex sym
else 
ifeq ($(LOADFORMAT),binary)
build: elf bin sym
else 
ifeq ($(LOADFORMAT),both)
build: elf hex bin sym
else 
$(error "$(MSG_FORMATERROR) $(FORMAT)")
endif
endif
endif

${OUTDIR}/InitMods.c: Makefile
	@echo $(MSG_MODINIT) $(call toprel, $@)
	@echo ${quote}// Autogenerated file${quote} > ${OUTDIR}/InitMods.c 
	@echo ${quote}${foreach MOD, ${MODNAMES}, extern unsigned int ${MOD}Initialize(void);}${quote}  >> ${OUTDIR}/InitMods.c
	@echo ${quote}void InitModules() {${quote} >> ${OUTDIR}/InitMods.c
	@echo ${quote}${foreach MOD, ${MODNAMES}, ${MOD}Initialize();}${quote}  >> ${OUTDIR}/InitMods.c
	@echo ${quote}}${quote} >> ${OUTDIR}/InitMods.c

# Program the device.
ifeq ($(FLASH_TOOL),OPENOCD)
# Program the device with Dominic Rath's OPENOCD in "batch-mode", needs cfg and "reset-script".
program: $(OUTDIR)/$(TARGET).elf
	@echo ${quote}Programming with OPENOCD${quote}
	$(OOCD_EXE) $(OOCD_CL)
endif

# Link: create ELF output file from object files.
$(eval $(call LINK_TEMPLATE, $(OUTDIR)/$(TARGET).elf, $(ALLOBJ)))

# Assemble: create object files from assembler source files.
$(foreach src, $(ASRC), $(eval $(call ASSEMBLE_TEMPLATE, $(src)))) 

# Assemble: create object files from assembler source files. ARM-only
$(foreach src, $(ASRCARM), $(eval $(call ASSEMBLE_ARM_TEMPLATE, $(src)))) 

# Compile: create object files from C source files.
$(foreach src, $(SRC), $(eval $(call COMPILE_C_TEMPLATE, $(src)))) 

# Compile: create object files from C source files. ARM-only
$(foreach src, $(SRCARM), $(eval $(call COMPILE_C_ARM_TEMPLATE, $(src)))) 

# Compile: create object files from C++ source files.
$(foreach src, $(CPPSRC), $(eval $(call COMPILE_CPP_TEMPLATE, $(src)))) 

# Compile: create object files from C++ source files. ARM-only
$(foreach src, $(CPPSRCARM), $(eval $(call COMPILE_CPP_ARM_TEMPLATE, $(src)))) 

# Compile: create assembler files from C source files. ARM/Thumb
$(eval $(call PARTIAL_COMPILE_TEMPLATE, SRC))

# Compile: create assembler files from C source files. ARM only
$(eval $(call PARTIAL_COMPILE_ARM_TEMPLATE, SRCARM))

.PHONY: elf lss sym hex bin
elf: $(OUTDIR)/$(TARGET).elf
lss: $(OUTDIR)/$(TARGET).lss 
sym: $(OUTDIR)/$(TARGET).sym
hex: $(OUTDIR)/$(TARGET).hex
bin: $(OUTDIR)/$(TARGET).bin

# Display sizes of sections.
$(eval $(call SIZE_TEMPLATE, $(OUTDIR)/$(TARGET).elf))
.PHONY: size
size: $(OUTDIR)/$(TARGET).elf_size

# Generate Doxygen documents
docs:
	doxygen  $(DOXYGENDIR)/doxygen.cfg

# Target: clean project.
clean: clean_list

clean_list :
	@echo $(MSG_CLEANING)
	$(V1) $(REMOVE) $(OUTDIR)/$(TARGET).map
	$(V1) $(REMOVE) $(OUTDIR)/$(TARGET).elf
	$(V1) $(REMOVE) $(OUTDIR)/$(TARGET).hex
	$(V1) $(REMOVE) $(OUTDIR)/$(TARGET).bin
	$(V1) $(REMOVE) $(OUTDIR)/$(TARGET).sym
	$(V1) $(REMOVE) $(OUTDIR)/$(TARGET).lss
	$(V1) $(REMOVE) $(ALLOBJ)
	$(V1) $(REMOVE) $(LSTFILES)
	$(V1) $(REMOVE) $(DEPFILES)
	$(V1) $(REMOVE) $(SRC:.c=.s)
	$(V1) $(REMOVE) $(SRCARM:.c=.s)
	$(V1) $(REMOVE) $(CPPSRC:.cpp=.s)
	$(V1) $(REMOVE) $(CPPSRCARM:.cpp=.s)


# Create output files directory
# all known MS Windows OS define the ComSpec environment variable
ifdef ComSpec
$(shell md $(subst /,\\,$(OUTDIR)) 2>NUL)
else
$(shell mkdir -p $(OUTDIR) 2>/dev/null)
endif

# Include the dependency files.
ifdef ComSpec
-include $(shell md $(subst /,\\,$(OUTDIR))\dep 2>NUL) $(wildcard $(OUTDIR)/dep/*)
else
-include $(shell mkdir -p $(OUTDIR) 2>/dev/null) $(shell mkdir $(OUTDIR)/dep 2>/dev/null) $(wildcard $(OUTDIR)/dep/*)
endif

# Listing of phony targets.
.PHONY : all build clean clean_list program
