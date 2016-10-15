# STM8S eForth (stm8ef)

This is a refactored port of Dr. C.H. Ting's eForth for the *STM8S Discovery* to STM8S *Value Line* devices like *STM8S003F3* and to the [SDCC toolchain](http://sdcc.sourceforge.net/), 
which makes mixing Forth, assembler, and C possible.

Most of the changes are related to providing "board support" for STM8S based low-cost Chinese made "gadgets", but there are also additions, like support for [background control tasks](https://github.com/TG9541/stm8ef/wiki/eForth-Background-Task).

Changes to the original code:

* SDCC tool chain "ASxxxx V2.0" syntax
* Usage of the SDCC linker with declaration of ISR routines in `main.c`
* hard STM8S105C6 dependencies removed (e.g. RAM layout, UART2)
* 1K RAM layout, meaningful symbols for RAM locations
* conditional code for different target boards
* some bugfixes (e.g. SEE better for "Subroutine Threaded")
* reduced Flash code size: a fully interactive Forth system uses below 5500 bytes, leaving 2.5 KB for new words!

New features:

* concurrent **cyclic INPUT-PROCESS-OUTPUT background tasks** with a fixed timebase (e.g. 5ms using TIM2) 
* support for [boards with 7Seg-LED UI](https://github.com/TG9541/stm8ef/wiki/eForth-Background-Task): in a background task, `123 .` goes to the 7Seg-LED display, and `?KEY` reads board keys
* words for board keys, ADC, outputs/relays/leds
* words for Flash, EEPROM, direct bit operations, inv. order 16bit memory access

## STM8S003F3/STM8S103F3 µC devices

The availability of low-cost boards (e.g. thermostats, power supplies, WIFI modules) makes the *STM8S003F3* the main target.

Some of the differences between STM8S003F3 and STM8S105C6T6 (*STM8S Discovery*) are:

* UART1 instead of UART2
* 8 KiB Flash instead of 32 KiB
* 1 KiB RAM instead of 2 KiB
* 128 or 640 bytes EEPROM (STM8S103F3) instead of 1 KiB
* reduced set of GPIO and other peripherals

## Board/module support:

* `MODULE_CORE` STM8S003F3 core, most extra feature words disabled 
* `MODULE_MINDEV` STM8S103F3 low cost "minimum development board"
* `MODULE_W1209` W1209 low cost thermostat with LED display and half-duplex RS232 through sensor header (9600 baud) 
* `MODULE_RELAY` C0135 "Relay-4 Board" (can be used as a *Nano PLC*)

Please refer to the [Wiki on GitHub](https://github.com/TG9541/stm8ef/wiki) for more information! 

There is board suport for some easily available "Chinese gadgets". For details, refer to [STM8S-Value-Line-Gadgets](https://github.com/TG9541/stm8ef/wiki/STM8S-Value-Line-Gadgets) in the Wiki.

### STM8S003F3 Core

STM8S003F3 core for new experiments.

Run `make BOARD=CORE flash` for building and flashing.

### STM8S103F3 "minimum development board"

Cheap STM8S103F3P6-based breakout board with LED on port B5 (there are plenty of vendors on EBay or AliExpress, the price starts below $0.70 incl. shipping)

* clock source internal 16 MHz RC oscillator `HSI`
* LED on GPIO PB5
* reset key

Run `make BOARD=MINDEV flash` for building and flashing.

### W1209 Thermostat Module

Low-cost STM8S003F3P6-based thermostat module with a 3 digit 7S-LED display, relay, and a 10k NTC sensor. 
This very cheap board can be easily used for single input/single output control tasks with UI (e.g. timer, counter, dosing, monitoring).

Run `make BOARD=W1209 flash` for building and flashing.

#### Note:

Interactive development is possible using half-duplex RS232 communication through the sensor header:
Port D6 (RxD) is on the NTC header, and communication is possible with a half-duplex "multiple access bus". 
The SW simulation for TX causes very little CPU overhead (9600 baud with TIM4).

Prerequists for using eForth on a W1209 interactively:

* remove the capacitor next to header (0.47µF is way too much for the UART) 
* on the terminal side, use wired-or *RXD || TxD*  /w open drain (e.g. USB CH340 with 1N4148 in series with TxD) 

Please refer to the [wiki](https://github.com/TG9541/stm8ef/wiki/STM8S-Value-Line-Gadgets#w1209).

### Relay Board-4

The board, sometimes labelled C0135 or "Relay Board-4" is a low cost PLC I/O expander with the following features:

* STM8S103F3P6 (640 bytes EEPROM) 
* 4 relays NC/NO rated 250VAC-10A (with red monitoring LEDs) on PB4, PC3, PC4, and PC5 
* 4 unprotected inputs (PC6, PC7, PD2, PD3, 2 usable as 3.3V analog-in), 
* 1 LED on PD4, 
* 1 key on PA3, 
* RS485 (PB5 enable - PD5 TX, PD6 RX on headers)
* 8MHz crystal (I use the 16 MHz HSI) 

Run `make BOARD=C0135 flash` for building and flashing.

### STM8S Discovery

I currently don't have a STM8S105C6T6 device for testing - the code is most likely brokend!

## Outlook

I'll be working on the code on-and-off as a hobby project, for connecting with the Forth world.  
Even if I like squezing the last microsecond out of time-critical code, please don't even think about using it for anything that requires safety, or dependability!

