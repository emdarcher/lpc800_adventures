#
# gcc Makefile for LPC810
# Kamal Mostafa <kamal@whence.com>
#
# License: Public Domain
#
# Modified by Ethan Durrant 2014

CROSS = arm-none-eabi-
CPU = -mthumb -mcpu=cortex-m0plus -MD
FPU = # -mfloat-abi=soft

LPC21ISP = lpc21isp
LPC21ISP_PORT = /dev/ttyUSB0
LPC21ISP_BAUD = 115200
LPC21ISP_CLK_KHZ = 14746

LPC810_DIR = ..
LPC810_LIB =  $(LPC810_DIR)/lib
LPC810_LIBINC_DIR = $(LPC810_LIB)/inc
LPC810_LIBSRC_DIR = $(LPC810_LIB)/src
CMSIS_DIR = $(LPC810_DIR)/cmsis
LD_DIR = $(LPC810_DIR)/ld
FILENAME = main
LINKERSCRIPT = $(LD_DIR)/LPC810.ld

CFLAGS+= -Os --specs=nano.specs -ffunction-sections -fdata-sections -fno-builtin

CC = ${CROSS}gcc
LD = ${CROSS}ld
OBJCOPY = ${CROSS}objcopy
SIZE = ${CROSS}size

# use these on the $(LD) line, as needed:
LIBM = ${shell ${CC} ${CFLAGS} --print-file-name=libm.a}
LIBC = ${shell ${CC} ${CFLAGS} --print-file-name=libc.a}
LIBGCC = ${shell ${CC} ${CFLAGS} --print-libgcc-file-name}

CFLAGS += $(CPU) $(FPU)
LDFLAGS += --gc-sections

CSRC = 
SRC = 
HEADERS = 

INC += -I$(CMSIS_DIR) -I$(LPC810_LIBINC_DIR) -I$(LPC810_LIBSRC_DIR)
CFLAGS += $(INC) -D__USE_CMSIS # -DCLOCK_SETUP=1
SRC_DIRS = $(LPC810_LIBSRC_DIR)
LPC810_LIBSRC = gpio.c \
		uart.c \
		mrt.c \
		printf.c \
		printf-retarget.c \
		system_LPC8xx.c \
		gcc_startup_lpc8xx.c

SRC += $(addprefix $(LPC810_LIBSRC_DIR)/, $(LPC810_LIBSRC))


CSRC = main.c
SRC += $(CSRC) 


## List of all header files
HEADERS = $(SRC:.c=.h)
## For every .c file, compile an .o object file
OBJ = $(SRC:.c=.o)
D_FILES = $(OBJ:.o=.d)
SRC_OBJS = $(OBJ)
#SRC_OBJS = main.o \
              gpio.o \
              uart.o \
              mrt.o \
              printf.o \
              printf-retarget.o \
              system_LPC8xx.o \
              gcc_startup_lpc8xx.o


all: $(FILENAME).bin

#blinky.axf: $(FILENAME).ld $(BLINKY_OBJS)
%.axf: $(LINKERSCRIPT) $(SRC_OBJS) 
#$(FILENAME).axf: $(LINKERSCRIPT) $(SRC_OBJS) 
	@$(LD) -o $@ $(LDFLAGS) -T $(LINKERSCRIPT) $(SRC_OBJS) $(LIBGCC)
	-@echo ""
	@$(SIZE) $@
	@cp $@ out.axf 
#$(FILENAME).axf
#	@cp $(FILENAME).axf out.axf
	@rm -f $(D_FILES) $(OBJ)
#$@ #%.axf

clean:
	@rm -f *.o *.d 
	@rm -f *.axf *.hex *.bin
#	@rm -f $(FILENAME).axf $(FILENAME).hex $(FILENAME).bin

%.bin:%.axf
	-@echo ""
	-@echo "Generating $(FILENAME).hex (FlashMagic, etc.)"
	@$(OBJCOPY) --strip-unneeded -O ihex $(FILENAME).axf $(FILENAME).hex
	-@echo "Generating $(FILENAME).bin"
	@$(OBJCOPY) --strip-unneeded -O binary $(FILENAME).axf $(FILENAME).bin


upload_serial:
	$(LPC21ISP) $(FILENAME).hex $(LPC21ISP_PORT) $(LPC21ISP_BAUD) $(LPC21ISP_CLK_KHZ)

size:
	$(SIZE) out.axf


