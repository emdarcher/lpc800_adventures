
OPTIMIZATION = s

# lpc810 / lpc811 / lpc812
MCU=lpc812

#change this to the path to the dir that contains the /core and /lib dirs
BASEDIR = ..

#########################################################################


PROJECT=main
LDSCRIPT=$(BASEDIR)/core/$(MCU).ld


LPC21ISP = lpc21isp
LPC21ISP_PORT = /dev/ttyUSB0
LPC21ISP_BAUD = 115200
LPC21ISP_CLK_KHZ = 30000
LPC21ISP_ARGS = $(LPC21ISP_PORT) $(LPC21ISP_BAUD) $(LPC21ISP_CLK_KHZ) 


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

SRC += $(CORE_SRC) $(LOCAL_SRC) $(DRIVERS_SRC)

HEADERS = $(wildcard  $(BASEDIR)/core/*.h)
OBJECTS= $(SRC:.c=.o)
DEPS= $(SRC:.c=.d)
#DEPS= $(HEADERS)
LSTFILES= $(SRC:.c=.lst)




#  Compiler Options
GCFLAGS = -std=gnu99  -mcpu=cortex-m0plus.small-multiply \
    -mthumb -O$(OPTIMIZATION) -I. -I$(COREDIR) -I$(DRIVERDIR)/inc
# Warnings
GCFLAGS += -Wno-strict-aliasing -Wstrict-prototypes -Wundef \
    -Wall -Wextra -Wunreachable-code 
# Optimizazions
GCFLAGS +=  -fstrict-aliasing -fsingle-precision-constant \
    -funsigned-char -funsigned-bitfields -fshort-enums \
    -fno-builtin -ffunction-sections -fno-common -fdata-sections 
# Debug stuff
GCFLAGS += -Wa,-adhlns=$(<:.c=.lst) -g

LDFLAGS = -T$(LDSCRIPT) -nostartfiles  -Wl,--gc-section 

NMFLAGS = -n -S 

#  Compiler/Linker Paths
GCC = arm-none-eabi-gcc
OBJCOPY = arm-none-eabi-objcopy
REMOVE = rm -f
SIZE = arm-none-eabi-size
NM = arm-none-eabi-nm
#########################################################################

all: $(PROJECT).bin size

%.bin: %.elf
	$(OBJCOPY) -R .stack -O binary $< $@
	 
%.elf:  $(SRC) 
	$(GCC)  $(GCFLAGS) $(LDFLAGS) $(SRC) --output $@

clean:
	rm *.bin *.elf

size: $(PROJECT).elf
	$(SIZE) $(PROJECT).elf

upload_serial:
	$(LPC21ISP) -bin $(PROJECT).bin $(LPC21ISP_ARGS) 

mem_use:
	$(NM) $(NMFLAGS) $(PROJECT).elf

.PHONY : clean all flash stats debug

