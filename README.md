AutoTuning FSR Trigger
======================

Arduino code to facilitate auto-tuning of a force sensing resistor for triggering end stops and auto probes for 3d printer auto-levelling.


FSR Module In Action
====================
* http://youtu.be/ziHav-B4uYc


What Are FSR(Force Sensing Resistors)?
======================================

FSR(s) are resistors that have conductive material sandwiched between two layers. When pressure is applied, the conductive bits come into contact in degrees relative to the amount of pressure exerted. The result is a resistor that starts with a high level of resistance and as presure is applied, the resistance drops.

Use Case?
=========

When used as a sensor in a 3d printer for the purpose of auto-probing, the sensor is either placed under the print bed, at the three contact points in the case of a delta, and are used to detect when the head touches the print bed. In this manner, a mechanical switch probe and probe stick aren't required for auto-probing operations.

What this code?
===============

Because of differences in print bed weights, build tolerances, and other factors, the output from the FSR can vary and the effective threshold for when a trigger event happens, can change with some other mechanical or physical tweak occuring with the printer. To this end, folks have used things like potentiometers, firmware tuning, and other discreet component methods to adjust the threshold level.

This code takes the approach of zeroing out for tare weight when applied to scales. The code will zero out and arrive at a "settled" level of pressure for a given moment in time. When sharp increase of pressure is detected, the setup will output a HIGH on the output feeding the z-MIN-STOP to simulate the auto-probe switch being triggered. If the HIGH state lasts longer than a TIMEOUT, the code will assume the situation has changed and will zero out the pressure reading again. 

The code is meant to be run on an ATtiny85 or similar MCU with a built in ADC and digital out. The Mhz speed isn't entirely critical save for the TIMEOUT handling.

Physical Setup
==============

* The FSR is wired between the 5V VCC source to the ADC input. 
* A resistor is wired from 0V GND to the ADC input to privide the drain to zero.
* The MCU(Attiny85/atmega328/arudino pro mini/etc) is flashed with the firmware that handles the logic.
* The MCU is hooked up to the 5V power supply and to GND. (3.3V will work as well, so long as all voltages are at the 3.3V level).
* An optional optical isolator can be used between the digital out of the MCU and the end stop connections to the 3d printer control board.

Configurable Variables In Code
================================

* TIMEOUT - how long to latch up the output signal to HIGH before deciding that it should re-settle on that being the new "normal". Defaults to 250ms (1/4 second).
* NOISE - variations of ADC reading to ignore and consider close enough to the average value to be considered "settled".
* THRESHOLD - an increase of this much above ambient will represent a "hit" or "contact" and will trigger a HIGH on the digital output, subject to the TIMEOUT value/behaviour listed above.

Credits/References/Shout Outs
=============================

* Thanks to the DeltaBot Google Group for introducing me to FSR(s)!
 * https://groups.google.com/forum/#!forum/deltabot
* Thanks to Rich for the awesome mod to the Marlin firmware to support the auto-probing.
 * https://github.com/RichCattell/Marlin

Getting Arduino To Work with ATTINY chips
=========================================

I got Arduino 1.5.x working with the following resource:

https://code.google.com/p/arduino-tiny/


Planned Features
================
* External switch to adjust behaviour/toggle
* Support for external LED or other indicator of internal acitivity
* Independent reading and levelling for 3 FSR sensors
* Independent status LED(s) for each sensor
* LED status indicator support
* Detect when FSR settles on a level that is too close to the ADC limit and alert

Got a feature you are interested in me incorporating? Please file an issue. Thanks!

CHANGELOG
==============

** March 30th, 2014 **
* Fixed board defines
* Verified working on ATtiny85 (single FSR)

** March 29th, 2014 **
* Reworked code to cleanup setup and main loop.
* Added configuration for single-shot sensor calibration vs continous sensor calibration
* Added definitions to support multiple FSR sensor inputs
* Added option to flip signal behaviour between LOW->HIGH  and HIGH->LOW
* Took out serial debug lines

** Initial Version **

Initial code commit. Continous sensor recalibration.
