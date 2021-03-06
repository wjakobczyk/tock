# Makefile for building the tock kernel for the CLUE nRF52840 board.

TOCK_ARCH=cortex-m4
TARGET=thumbv7em-none-eabi
PLATFORM=clue_nrf52840

include ../Makefile.common

ifdef PORT
  FLAGS += -p $(PORT)
endif

KERNEL=$(TOCK_ROOT_DIRECTORY)target/$(TARGET)/debug/$(PLATFORM).elf
KERNEL_WITH_APP=$(TOCK_ROOT_DIRECTORY)/target/$(TARGET)/debug/$(PLATFORM)-app.elf

# Upload the kernel using nrfutil
.PHONY: program program-apps

program: $(TOCK_ROOT_DIRECTORY)target/$(TARGET)/debug/$(PLATFORM).bin
	ifndef PORT
		$(error Please specify the serial port using the PORT variable) 
	endif
	adafruit-nrfutil dfu genpkg --dev-type 0x0052 --sd-req 0x00B6 --application $(TOCK_ROOT_DIRECTORY)target/$(TARGET)/debug/$(PLATFORM).bin $(KERNEL).zip
	echo "Trying to reset device"
	stty -F $(PORT) 1200 && sleep 0.5 && stty -F $(PORT) 1200
	adafruit-nrfutil --verbose dfu serial -pkg $(KERNEL).zip $(FLAGS) -b 115200 --singlebank

# Upload the kernel and apps using nrfutil
program-apps: $(TOCK_ROOT_DIRECTORY)target/$(TARGET)/debug/$(PLATFORM).elf
	ifndef PORT
		$(error Please specify the serial port using the PORT variable) 
	endif
	ifndef APP
		$(error Please define the APP variable with the TBF file to flash an application) 
	endif
	arm-none-eabi-objcopy --update-section .apps=$(APP) $(KERNEL) $(KERNEL_WITH_APP)
	arm-none-eabi-objcopy --output-target=binary -S $(KERNEL_WITH_APP) $(KERNEL_WITH_APP).bin
	adafruit-nrfutil dfu genpkg --dev-type 0x0052 --sd-req 0x00B6 --application $(KERNEL_WITH_APP).bin $(KERNEL_WITH_APP).zip
	echo "Trying to reset device"
	stty -F $(PORT) 1200 && sleep 0.5 && stty -F $(PORT) 1200
	adafruit-nrfutil --verbose dfu serial -pkg $(KERNEL_WITH_APP).zip $(FLAGS) -b 115200 --singlebank
