
# Marlin 3D Printer Firmware for Anycubic I3 Mega
## with Original TFT display
### Based on work form Murdock (lesimprimantes3d.fr)
### Based on work from derhopp (https://github.com/derhopp) and Bartolomeus (Thingiverse)

MARLIN 1.1.8 Anycubic I3 Mega V2 with original screen.

Firmware for the mega v2 (no ABL, dual Z and 8bits trigorilla card) which is a mix of the work of Murdock (lesimprimates3d.fr) for TFT32 MKS screen and the work of Derhopp from thingiverse.

This version allows you to switch to 1.1.8 by keeping the original screen of the printer.

To install it, it's as usual, with arduino for compilation and then push everything on the printer. E.g. do the .hex file and send it with Octoprint (via the firmware plugin).

The values in the conf file for tuning or the extruder steps are normally good to start with. If needed, a small M502 and M500 to update the EEPROM can be required.

To go back to stock, just put the original Anycubic file (from their website).

Difference compared to anycubic official firmware:
* No start music
* No recovery after power failure
* Thermal runaway active
* Linear advice active (K 150)
* ABL linear active (Touch mi setup)
* Hotend limitation at 250°. (can be too small for petg and ABS but good limit for V5 hotend)
* Hotend fan can run at 12V (maybe put 85-90% instead of 100% for the fan otherwise it cool to much)
* Special menu from Derhopp active. New entry in the menu to put hotend in maintenance position
* More tests to do to see if everything is ok

************
New things on top of regular  marlin:

DerHopp made a special menu in the SD part. If you go to the print menu, you can select "Special Menu" at the top and press the round / arrow button to enter the menu.

The “genius” idea of the developer is to use the file selection screen on the SD to generate an additional menu.

It adds to the top of the list of files an entry that looks like a directory and is called "special menu". When we go in there there is a list of possible action. You already have some configured entries (read eeprom, save eeprom, auto tune ...). It compensates for the lack of customization of the lcd. And it's easy to add new ones if necessary as for lighting LEDs for example.

If you want to add / modify these entries, you have to edit the firmware and send it back to the trigorilla. Everything happens in the AnycubicTFT.cpp file:
in the AnycubicTFTClass :: Ls () section we add the menu entries (4 per page) of the <Auto Tune Hotend PID> style and then in the AnycubicTFTClass :: HandleSpecialMenu () section we define which command (s) will be started : M303 C8 S200.

************

# FAQ

###### My special menu does not work :

In order to use the menu, a SD card must be in the printer. When choosing entry, when it turns red, you should validate your choice with the "rounded arrow" button

###### My special menu hang :

Please remove the Chinese folders/files from the SD card

###### My printer stop just after first layer :

As the fan is now capable to go to 100% when it start (generally after the first layer) then it is possible that the hotend temperature drops too much and beyond the thermal runaway configured in the firmware. Two options here : change slicer setting to move avoid 0->100% and have maybe second layer at 80 then third one to 90 and so on. Otherwise, you can as well change the firmware setting and compile it again.

###### My print quality does not look good :

You need to perform few optional operations to make sure the settings are accurate:

1. Reset the EEPROM settings (to start fresh with firmware values)

 Just do a M502 to restore factory firmware value and a M500 to save them.

 Send: M502 Recv: echo:Hardcoded Default Settings Loaded

 Send: M500 Recv: echo:Settings Stored (614 bytes; crc 3761)

2. Auto Tune PID

 PID Tuning: We start cold and we do a: M303 E0 S200 C8 U1

 We see the values found at the end of the test:

 Recv: PID Autotune finished!

 #define DEFAULT_Kp 14.23

 #define DEFAULT_Ki 1.03

 #define DEFAULT_Kd 49.32

 and we save with an M500

 (U1 directive allow to remove the need of manual update via a: M301 P14.23 I1.03 D49.32)

3. Change Step values for extruder if required

 We write down the current value of the steps with the command M503 and we look at the value found for the M92 behind the parameter E (92.6 by default on the i3)
 To adjust the extruder, it makes a mark on the filament from the extruder at 15 cm for example.

 Reset to zero the extrusion counter with a G92 E0
 Then the hotend is heated and the following command is used to push the filament by 10 cm: G1 E10 F 92

 Then we measure how much centimeters it remains between the mark and the extruder and we calculate the new value of step: Ex: 9.1 cm are taken instead of 10 so the value will be 92.6 * 100/91 = 102 (old value * 100 / number of cm taken).

 We send the new value in the printer: M92 E102

 We save the values: M500

4. Change the hotted fan speed.

 As the fan is now capable to go to 100% when it start (generally after the first layer) then it is possible that the hotend temperature drops too much and beyond the thermal runaway configured in the firmware.

 Two options here : change slicer setting to move avoid 0->100% and have maybe second layer at 80 then third one to 90 and so on. Otherwise, you can as well change the firmware setting and compile it again.

 This is in the configuration_adv file :

 * if ENABLED(THERMAL_PROTECTION_HOTENDS)
 * define THERMAL_PROTECTION_PERIOD 40        // Seconds
 * define THERMAL_PROTECTION_HYSTERESIS 4     // Degrees Celsius

 Please note that the official firmware has this setting "disable" which is a huge risk.

 Documentation state :

  * Thermal Protection provides additional protection to your printer from damage
  * and fire. Marlin always includes safe min and max temperature ranges which
  * protect against a broken or disconnected thermistor wire.

  * The issue: If a thermistor falls out, it will report the much lower
  * temperature of the air in the room, and the the firmware will keep
  * the heater on.

  * The solution: Once the temperature reaches the target, start observing.
  * If the temperature stays too far below the target (hysteresis) for too
  * long (period), the firmware will halt the machine as a safety precaution.

  * If you get false positives for "Thermal Runaway", increase
  * THERMAL_PROTECTION_HYSTERESIS and/or THERMAL_PROTECTION_PERIOD

5. Change the K value for linear advance.

 Value for K is set at 150. This is generally a good value for PLA. You can use the gcode generator on marlin website if you want to adjust the value to your printer/filament. (http://marlinfw.org/docs/features/lin_advance.html)
Command to change it is M900 Kxx (where xx is what you found with the test)


***
***
***
***

# Marlin 3D Printer Firmware
<img align="right" src="../../raw/1.1.x/buildroot/share/pixmaps/logo/marlin-250.png" />

## Marlin 1.1

Marlin 1.1 represents an evolutionary leap over Marlin 1.0.2. It is the result of over two years of effort by several volunteers around the world who have paid meticulous and sometimes obsessive attention to every detail. For this release we focused on code quality, performance, stability, and overall user experience. Several new features have also been added, many of which require no extra hardware.

For complete Marlin documentation click over to the [Marlin Homepage <marlinfw.org>](http://marlinfw.org/), where you will find in-depth articles, how-to videos, and tutorials on every aspect of Marlin, as the site develops. For release notes, see the [Releases](https://github.com/MarlinFirmware/Marlin/releases) page.

## Stable Release Branch

This Release branch contains the latest tagged version of Marlin (currently 1.1.8 – December 2017).

Previous releases of Marlin include [1.0.2-2](https://github.com/MarlinFirmware/Marlin/tree/1.0.2-2) (December 2016) and [1.0.1](https://github.com/MarlinFirmware/Marlin/tree/1.0.1) (December 2014). Any version of Marlin prior to 1.0.1 (when we started tagging versions) can be collectively referred to as Marlin 1.0.0.

## Contributing to Marlin

Click on the [Issue Queue](https://github.com/MarlinFirmware/Marlin/issues) and [Pull Requests](https://github.com/MarlinFirmware/Marlin/pulls) links above at any time to see what we're currently working on.

To submit patches and new features for Marlin 1.1 check out the [bugfix-1.1.x](https://github.com/MarlinFirmware/Marlin/tree/bugfix-1.1.x) branch, add your commits, and submit a Pull Request back to the `bugfix-1.1.x` branch. Periodically that branch will form the basis for the next minor release.

Note that our "bugfix" branch will always contain the latest patches to the current release version. These patches may not be widely tested. As always, when using "nightly" builds of Marlin, proceed with full caution.

## Current Status: In Development

Marlin development has reached an important milestone with its first stable release in over 2 years. During this period we focused on cleaning up the code and making it more modern, consistent, readable, and sensible.

## Future Development

Marlin 1.1 is the last "flat" version of Marlin!

Arduino IDE now has support for folder hierarchies, so Marlin 1.2 will have a [hierarchical file structure](https://github.com/MarlinFirmware/Marlin/tree/breakup-marlin-idea). Marlin's newly reorganized code will be easier to work with and form a stronger starting-point as we get into [32-bit CPU support](https://github.com/MarlinFirmware/Marlin/tree/32-Bit-RCBugFix-new) and the Hardware Access Layer (HAL).

[![Coverity Scan Build Status](https://scan.coverity.com/projects/2224/badge.svg)](https://scan.coverity.com/projects/2224)
[![Travis Build Status](https://travis-ci.org/MarlinFirmware/Marlin.svg)](https://travis-ci.org/MarlinFirmware/Marlin)

## Marlin Resources

- [Marlin Home Page](http://marlinfw.org/) - The Marlin Documentation Project. Join us!
- [RepRap.org Wiki Page](http://reprap.org/wiki/Marlin) - An overview of Marlin and its role in RepRap.
- [Marlin Firmware Forum](http://forums.reprap.org/list.php?415) - Find help with configuration, get up and running.
- [@MarlinFirmware](https://twitter.com/MarlinFirmware) on Twitter - Follow for news, release alerts, and tips & tricks. (Maintained by [@thinkyhead](https://github.com/thinkyhead).)

## Credits

The current Marlin dev team consists of:
 - Roxanne Neufeld [[@Roxy-3D](https://github.com/Roxy-3D)]
 - Scott Lahteine [[@thinkyhead](https://github.com/thinkyhead)]
 - Bob Kuhn [[@Bob-the-Kuhn](https://github.com/Bob-the-Kuhn)]

Notable contributors include:
 - Alberto Cotronei [[@MagoKimbra](https://github.com/MagoKimbra)]
 - Andreas Hardtung [[@AnHardt](https://github.com/AnHardt)]
 - Bernhard Kubicek [[@bkubicek](https://github.com/bkubicek)]
 - Bob Cousins [[@bobc](https://github.com/bobc)]
 - Chris Palmer [[@nophead](https://github.com/nophead)]
 - David Braam [[@daid](https://github.com/daid)]
 - Edward Patel [[@epatel](https://github.com/epatel)]
 - Erik van der Zalm [[@ErikZalm](https://github.com/ErikZalm)]
 - Ernesto Martinez [[@emartinez167](https://github.com/emartinez167)]
 - F. Malpartida [[@fmalpartida](https://github.com/fmalpartida)]
 - Jochen Groppe [[@CONSULitAS](https://github.com/CONSULitAS)]
 - João Brazio [[@jbrazio](https://github.com/jbrazio)]
 - Kai [[@Kaibob2](https://github.com/Kaibob2)]
 - Luc Van Daele[[@LVD-AC](https://github.com/LVD-AC)]
 - Nico Tonnhofer [[@Wurstnase](https://github.com/Wurstnase)]
 - Petr Zahradnik [[@clexpert](https://github.com/clexpert)]
 - Thomas Moore [[@tcm0116](https://github.com/tcm0116)]
 - [[@alexxy](https://github.com/alexxy)]
 - [[@android444](https://github.com/android444)]
 - [[@benlye](https://github.com/benlye)]
 - [[@bgort](https://github.com/bgort)]
 - [[@ejtagle](https://github.com/ejtagle)]
 - [[@Grogyan](https://github.com/Grogyan)]
 - [[@marcio-ao](https://github.com/marcio-ao)]
 - [[@maverikou](https://github.com/maverikou)]
 - [[@oysteinkrog](https://github.com/oysteinkrog)]
 - [[@p3p](https://github.com/p3p)]
 - [[@paclema](https://github.com/paclema)]
 - [[@paulusjacobus](https://github.com/paulusjacobus)]
 - [[@psavva](https://github.com/psavva)]
 - [[@Tannoo](https://github.com/Tannoo)]
 - [[@teemuatlut](https://github.com/teemuatlut)]
 - ...and many others

## License

Marlin is published under the [GPL license](https://github.com/COPYING.md) because we believe in open development. The GPL comes with both rights and obligations. Whether you use Marlin firmware as the driver for your open or closed-source product, you must keep Marlin open, and you must provide your compatible Marlin source code to end users upon request. The most straightforward way to comply with the Marlin license is to make a fork of Marlin on Github, perform your modifications, and direct users to your modified fork.

While we can't prevent the use of this code in products (3D printers, CNC, etc.) that are closed source or crippled by a patent, we would prefer that you choose another firmware or, better yet, make your own.

[![Flattr this git repo](http://api.flattr.com/button/flattr-badge-large.png)](https://flattr.com/submit/auto?user_id=ErikZalm&url=https://github.com/MarlinFirmware/Marlin&title=Marlin&language=&tags=github&category=software)
