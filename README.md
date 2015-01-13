# The Frienduino
The Frienduino is an Arduino clone you can make for a friend once you have any kind of Arduino and some soldering skill.
The board is easy to make (single sided) and easy to solder (only through hole components).
It has everything to get started yet it is cheap and simple because it saves on stuff the novice doesn't need (separate power supply and 3.3V regulator).

IMHO, what made the Arduino so popular is its USB connectivity. This lower the entry barrier to near zero.
No messing around with programmers, no legacy serial port required.
Alas, most of the many many Arduino clones out there lack this key feature.
I guess the reason for this is that, until now, all USB to serial adapters were not available as a through hole component, 
thus discouraging the novice to build a "true to the spirit" clone on their own.

It may seem odd at first to use a programmable micro controller just as a dumb USB to serial converter, but if you look at the price tag and consider it's the only way to get USB to serial as a through hole part it makes a lot of sense to me.
And not to me alone. The folks over at UNL Maker Club had a must have had a similar reasoning when they chose the 16F1454 as the USB adapter for their [Innovation Board](http://make.unl.edu/innovation-board/) all SMD Arduino clone.


## Design
The schematic and PCB are a copy of the [Arduino Duemilanove](http://arduino.cc/en/pmwiki.php?n=Main/arduinoBoardDuemilanove) reference design modified to be a single sided layout using the 16F1454 as the USB adapter and striped off the external power supply. Reset button and ICSP header were moved for better accessability and less complicated routing respectively.

## Flashing the PIC 16F1454
The pre-compiled firmware to turn the PIC 16F1454 into a serial adapter can be downloaded from [this](https://github.com/jgeisler0303/PIC16F1454_USB2Serial) repository. To flash the .hex-file to the PIC you can use a second Arduino board plus a 9V battery, a transistor (BC 548), two 4.7k resistors, and some jumper cables using the soft- and firm ware from [this](https://github.com/jgeisler0303/ardpicprog) repository. 
First, you have to flash your second Arduino with the special PIC-flashing firmware found in the repo's "ProgramPIC" folder. Then you have to compile the flashing host program in the "host" folder by typing `make` in the console (this is assuming you have gcc resp. MinGW installed).
Then you have to connect
* the transistor's collector via a 4.7k resistor to the plus pole of the battery and directly to pin 1 of the PICs 5x1 ICSP header on your board
* the transistor's base via a 4.7k resistor to your second Arduino's analog pin 1 (not 0)
* the transistor's emitter to ground
* the Frienduino's ground (pin 3 of the PICs ICSP header) to the second Arduino's ground
* the Frienduino's +5V (pin 2 of the PICs ICSP header) to digital pin 2 of your second Arduino
* pin 4 (data) of the PICs ICSP header to digital pin 7 of your second Arduino
* pin 5 (clock) of the PICs ICSP header to digital pin 4 of your second Arduino

Then you have to run `./ardpicprog -p /dev/ttyACM0 --erase --burn --input-hexfile  PIC16F1454_USB2Serial.hex` in Linux, or `ardpicprog.exe -p COM1 --erase --burn --input-hexfile  PIC16F1454_USB2Serial.hex` in Windows in a console in the flashing program's folder (assuming you copied the firmware hex file to this folder, and that the second Arduino is connected to the /dev/ttyACM0 resp. COM1 port). The flashing program will then identify your PIC as a 16F1454, erase it and then burn the new firmware (this will take a while). When the flashing program finishes you're done and can disconnect all jumper cables before you move on to flash the Atmega.

## Flashing the Atmega
In order to make our Atmega Arduino compatible you have to flash it with the Arduino bootloader. To do this, you can use a second Arduino board and follow these [instructions](http://arduino.cc/en/Tutorial/ArduinoISP). In the Arduino IDE you have to choose "Arduino as ISP" for the programmer and "Arduino Duemilanove w/ Atmega328" as the board.

