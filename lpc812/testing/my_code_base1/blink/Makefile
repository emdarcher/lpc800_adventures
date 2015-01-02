
OPTIMIZATION = s

# lpc810 / lpc811 / lpc812
MCU=lpc812

BASEDIR = ..
#########################################################################
PROJECT=main
LDSCRIPT=$(BASEDIR)/core/$(MCU).ld


LPC21ISP = lpc21isp
LPC21ISP_PORT = /dev/ttyUSB0
LPC21ISP_BAUD = 115200
LPC21ISP_CLK_KHZ = 30000

COREDIR = $(BASEDIR)/core
DRIVERDIR = $(BASEDIR)/drivers

#put the different drivers .c files that you are using in a list here
DRIVERS_FILES = 

SRC= 
OBJ = 
CORE_SRC = $(wildcard  $(BASEDIR)/core/*.c)
LOCAL_SRC = $(wildcard *.c)
DRIVERS_SRC = $(addprefix $(DRIVERDIR)/src/, $(DRIVERS_FILES))
#DRIVERS_SRC = $(wildcard  $(BASEDIR)/drivers/src/*.c)

#SRC+=$(wildcard  $(BASEDIR)/core/*.c) $(wildcard *.c) 

#OBJECTS=$(patsubst %,.bin/%,$(abspath $(SRC:.c=.o)))
#DEPS=$(patsubst %,.bin/%,$(abspath (SRC:.c=.d)))
#LSTFILES=$(patsubst %,.bin/%,$(abspath $(SRC:.c=.lst)))
#OBJECTS=$(patsubst %,.bin%,$(realpath $(SRC:.c=.o)))
#DEPS=$(patsubst %,.bin%,$(realpath $(SRC:.c=.d)))
#LSTFILES=$(patsubst %,.bin%,$(realpath $(SRC:.c=.lst)))

SRC += $(CORE_SRC) $(LOCAL_SRC) $(DRIVERS_SRC)

HEADERS = $(wildcard  $(BASEDIR)/core/*.h)
OBJECTS= $(SRC:.c=.o)
DEPS= $(SRC:.c=.d)
#DEPS= $(HEADERS)
LSTFILES= $(SRC:.c=.lst)


#SRC+=$(addprefix $(BASEDIR)/, 


#  Compiler Options
GCFLAGS = -std=gnu99  -mcpu=cortex-m0plus.small-multiply -mthumb -O$(OPTIMIZATION) -I. -I$(COREDIR) -I$(DRIVERDIR)/inc
# Warnings
GCFLAGS += -Wno-strict-aliasing -Wstrict-prototypes -Wundef -Wall -Wextra -Wunreachable-code 
# Optimizazions
GCFLAGS +=  -fstrict-aliasing -fsingle-precision-constant -funsigned-char -funsigned-bitfields -fshort-enums -fno-builtin -ffunction-sections -fno-common -fdata-sections 
# Debug stuff
GCFLAGS += -Wa,-adhlns=$(<:.c=.lst) -g

LDFLAGS = -T$(LDSCRIPT) -nostartfiles  -Wl,--gc-section 

#  Compiler/Linker Paths
GCC = arm-none-eabi-gcc
OBJCOPY = arm-none-eabi-objcopy
REMOVE = rm -f
SIZE = arm-none-eabi-size

#########################################################################

#-include $(DEPS)

all: $(PROJECT).bin size

%.bin: %.elf
	$(OBJCOPY) -R .stack -O binary $< $@

#$(OBJCOPY) -r .stack -o binary $< $@	
	 
#%.elf: $(SRC) $(OBJECTS) $(DEPS) 
%.elf:  $(SRC) 
#	$(GCC) -o $@ $(OBJECTS) $(GCFLAGS) $(LDFLAGS)
#	$(GCC) -o $@ $(SRC) $(GCFLAGS) $(LDFLAGS)
	$(GCC)  $(GCFLAGS) $(LDFLAGS) $(SRC) --output $@
#	$(GCC)  $(GCFLAGS) $(LDFLAGS) $(OBJECTS) --output $@

clean:
	rm *.bin *.elf

#
#all: .bin .bin/core firmware.bin stats
#
#.bin:
#	mkdir .bin
#
#.bin/core:
#	mkdir .bin/core
#
#firmware.bin: .bin/$(PROJECT).elf Makefile
#	@$(objcopy) -r .stack -o binary .bin/$(project).elf firmware.bin
#
#.bin/$(PROJECT).elf: $(OBJECTS) Makefile
#	@echo "  \033[1;34mLD \033[0m (\033[1;33m$(OBJECTS)\033[0m) -> $(PROJECT).elf"
#	@$(GCC) -o .bin/$(PROJECT).elf $(OBJECTS) $(GCFLAGS) $(LDFLAGS) 
#
#stats: .bin/$(PROJECT).elf 
#	@$(SIZE) .bin/$(PROJECT).elf
#
#clean:
#	@echo "  \033[1;34mCleanup\033[0m $<"
#	@$(REMOVE) $(OBJECTS)
#	@$(REMOVE) $(DEPS)
#	@$(REMOVE) $(LSTFILES)
#	@$(REMOVE) firmware.bin
#	@$(REMOVE) .bin/$(PROJECT).elf
#	@$(REMOVE) -d .bin/core
#	@$(REMOVE) -d .bin
#
#-include $(DEPS)
#
##########################################################################
#
#.bin/%.o: %.c Makefile 
#	@echo "  \033[1;34mGCC\033[0m $<"
#	@$(GCC) $(GCFLAGS) -o $@ -c $<
#	@$(GCC) $(GCFLAGS) -MM $< > $*.d.tmp
#	@sed -e 's|.*:|.bin/$*.o:|' < $*.d.tmp > .bin/$*.d
#	@sed -e 's/.*://' -e 's/\\$$//' < $*.d.tmp | fmt -1 | \
#		sed -e 's/^ *//' -e 's/$$/:/' >> .bin/$*.d
#	@rm -f $*.d.tmp
#
##########################################################################

size: $(PROJECT).elf
	$(SIZE) $(PROJECT).elf

#flash: all
#	tools/isp.py
#	-/Applications/lpcxpresso_5.1.2_2065/lpcxpresso/bin/dfu-util -d 0x471:0xdf55 -c 0 -t 2048 -R -D /Applications/lpcxpresso_5.1.2_2065/lpcxpresso/bin/LPCXpressoWIN.enc
#	sleep 1
#	/Applications/lpcxpresso_5.1.2_2065/lpcxpresso/bin/crt_emu_cm3_gen -wire=winusb -pLPC812 -vendor=NXP -flash-load-exec=firmware.bin

#debug: all
#	-/Applications/lpcxpresso_5.1.2_2065/lpcxpresso/bin/dfu-util -d 0x471:0xdf55 -c 0 -t 2048 -R -D /Applications/lpcxpresso_5.1.2_2065/lpcxpresso/bin/LPCXpressoWIN.enc
#	sleep 1
#	arm-none-eabi-gdb --eval-command="target extended-remote |/Applications/lpcxpresso_5.1.2_2065/lpcxpresso/bin/crt_emu_cm3_gen -wire=winusb -pLPC812 -vendor=NXP" .bin/$(PROJECT).elf
#

upload_serial:
	$(LPC21ISP) -bin $(PROJECT).bin $(LPC21ISP_PORT) $(LPC21ISP_BAUD) $(LPC21ISP_CLK_KHZ)

.PHONY : clean all flash stats debug

