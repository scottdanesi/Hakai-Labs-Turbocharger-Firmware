# Hakai-Labs-Turbocharger-Firmware
This is the official open source Hakai Labs Turbocharger Firmware

# HAKAI LABS – Turbocharger Firmware Update Instructions – v1.0

Thank you for your purchase of a Hakai Labs Turbocharger Module! This instruction manual will help guide you through how to update the firmware on your module in the event there is a new update available. We will never charge for future updates on our modules and we may release alternate versions for those of you who want a more customized experience.

## What is Needed to Update Your Firmware?

- Windows PC
- A USB to Serial UPDI Interface
  - The most common one that we use is the UPDI Friend from Adafruit. You can get that at the link below:
    - https://www.adafruit.com/product/5893
- JST SH 1mm Pitch 3 Pin to Socket Headers Cable
  - https://www.adafruit.com/product/5765
- A USB C cable
- The Hex file of the actual firmware for the module

## How to Identify Current Firmware Version

When the module is first powered on, it has a very bright green LED light on the back (sorry, was not supposed to be that bright, I will fix this in future hardware runs). This LED will blink with a specific pattern with a certain number of long blinks, followed by a certain number of quick blinks. The long blinks are the major version number, and the short blinks are the revision of that major version. So, if your module has v2.1 on it. It will blink 2 long blinks followed by 1 quick blink.

## How to Program Your Module

1. Set up your USB to Serial UPDI Programmer and attach the output Ground (GND), UPDI, and PWR (5v) to the appropriate pins on your modules UPDI header (3 pin) on the back side. **Please be very careful to match these connections up exactly as they are stated on the silkscreen. Hooking these up in reverse could damage your module.**
2. Set the switch on your UPDI Programmer to 5v.
3. Connect your UPDI programmer to your windows computer
4. Extract all contents of the firmware zip file to your hard drive in a location you will be able to find it later. For this example, let's say it is "C:\Hakai Labs Firmware".
5. Open that folder in Windows Explorer and run the "Flash_Turbocharger_2.1.bat" file. This will scan through the ports on your computer and find the Turbocharger module and update it for you.
6. Done! Enjoy your updated module.
