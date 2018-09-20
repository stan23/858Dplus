
Once the device (cold, wand in cradle) is turned on, it will do the following:
==============================================================================

* Display the firmware version
* Start the fan for about 2 seconds and turn it off again
* Either display "---" if the temperature is below 40°C (safe to touch)
* Or display the current temperature if the temperature is above 40°C


Fan controls:
=============

* The fan-speed is set with the potentiometer
* If the fan-speed is erratic, you need a new potentiometer
* If the wand is in the cradle and is cold (< 45°C), then fan is off
* If the wand is in the cradle and is too hot (> 60°C), the fan is on
* If the wand is out of the cradle, the fan is always on


Temperature control:
====================

* The base unit usually shows the current temperature (averaged)
* It shows the set-point, if the temperature is within +- 3°C
* Temperature can be changed with the up/down buttons
* Short button press: +- 1°C
* Long button press:  +- 10°C
* The set-point is stored in eeprom
* Temperature range: 50...500 °C
* Fan Only mode (press both buttons together shortly)


Settings:
=========

* Config mode is entered by pressing both buttons _at the same time_ 
  for a longer time
* When Settings are changed, the heater is always off
* The state of the fan is not changed when entering config mode
* The display will show an abbreviated name for each parameter
* Going to the next one is done by pressing both buttons _at the same time_

| Display | Setting           | Range      |
|---------|-------------------|:----------:|
|       p |            p_gain |    0...999 |
|       i |            i_gain |    0...999 |
|       d |            d_gain |    0...999 |
|     ith |       i_threshold |    0...100 |
|     tof |  temp_offset_corr | -100...100 |
|     tgn |    temp_gain_corr |  100...999 |
|     avg |     temp_averages |  100...999 |
|     slp |       slp_timeout |    0...30  |
|     fcl |   fan_current_min |    0...999 |
|     fch |   fan_current_max |    0...999 |
|     fsl |     fan_speed_min |  120...180 |
|     fsh |     fan_speed_max |  300...400 |
|     adc |   display_adc_raw |    0...1   |

* fsl/fsh and fcl/fch are depending on CURRENT_SENSE_MOD compile time option

* All parameters stored / loaded from EEPROM are checked if within range
* Invalid parameters are reset to their default values when stored / loaded
  (see youyue858d.h)

### i_threshold
temperature range (+-) that determines the switch between PD and PID
* if the temperature is far away from the set-point, PD-control is used
* if the temperature is within range, PID-control is used to stabilize

### temp_offset_corr & temp_gain_corr
* can be used to correct a temperature difference between the display and the temperature measured with an external sensor at the nozzle


### display_adc_raw
* display the raw ADC value instead of derived temperature
* temperature regulation works as usual
* very useful for creating calibration data (T-measured vs. ADC value)


Fan Speed/Current:
==================

* Can be tested when pressing up while powering on, current value is shown and updated.
* Hard reset to leave


Restoring default:
==================

* Turn the device off
* Press and hold both buttons at the same time
* Turn the device on and wait a couple of seconds
* The device will enter the PID settings menu with default value restored
* Either change the values again or turn the device off


Sleep timeout:
==============

SLP)     slp_timeout  0...30 (default: 5)

If the wand is not placed into the cradle for longer than slp_timeout, the heater
is switched off, but the fan keeps running indefinitely.


Error behaviour:
================

* If the wand is disconnected or the thermo-couple has failed or the temperature
  is above MAX_TEMP_ERR (550°C), the device will go into a "safe mode" and 
  display the error message "°C" + "Err".
  
  The heater will be turned off permanently, the fan will be turned on 
  to allow fast cool-down and the display will blink "*C" and "ERR" until power is cycled.

* If the device is powered up and the wand is not in the cradle, an error message
  will be displayed ("CrA" + "dlE"). Put the wand into the cradle for normal operation.

  This should avoid any issues with the device being accidentally powered on 
  with the wand on the table (e.g. after power outage).

* If the fan-speed on power-up (fan-test) is out of range (F-L, F-h config parameters),
  an error message "FAN" + "SPd" or "FAN" + "Cur" will be displayed.

  (This does not necessarily detect a stalled fan, as the feedback is just the
   drive voltage or current, not an actual tacho signal!)

  If you get this error on every startup with a low or high fan speed, adjust the config
  parameters for fsl/fsh (speed) or fcl/fch (current)

* If the WATCHDOG is used (by default) and the device becomes unresponsive for more
  than about 120ms AND the heater is ON, a watchdog-reset is triggered.

  THIS SHOULD NEVER HAPPEN.

  The device will turn off the heater and turn on the fan. The display will blink
  the error code "rSt" until power is cycled.

* If the "CKDIV8" FUSE bit is erroneously set when programming the firmware
(F_CPU is divided by 8), cause a watchdog reset. See previous entry for more info.
