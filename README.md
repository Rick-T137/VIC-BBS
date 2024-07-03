# VIC-BBS
VIC-20 Bulletin Board System  

This is a bulletin board system (BBS) that runs on a Commodore VIC-20 personal computer. In addition to the VIC, you'll need:
- at least 16KB of memory expansion (35KB expansion is optimal)
- a WiFi modem such as Strikelink (or some other method to connect ie: null modem to tcpser server)
- storage device (ie: 1541 disk drive, SD2IEC drive or pi1541 drive)

## Quick Start
- Download the latest appropriate D64 image from this repo (BBS.d64 is for a physical VIC-20, BBS-VICE.d64 is for a VICE-emulated VIC-20)
- If using emulation, load the D64 image in VICE, configure VICE's RS232 parameters, and ensure tcpser is running with the correct parameters.
  - VICE EXAMPLE:
  - Settings > Peripheral Devices > RS232
  - Enable Userport RS232 Emulation [checked]
  - RS-232 Interface Type [Normal Control Lines]
  - Device: Serial 3, Baud: 1200
  - Serial 3: 127.0.0.1:25232, Baud: 1200, IP232 [checked]
  - tcpser command line: tcpser -v 25232 -s 1200 -l 4 -p 6502 -i "e0s1s0=1s2=255s30=30&k0"
- If using a real VIC-20, transfer the BBS, BBS.ML and SYSOP programs to a 1541 diskette and ensure set your WiFi modem to answer calls.
  - WIFI MODEM EXAMPLE:
  - set baud rate to 1200
  - set "e0s1s0=1s2=255s30=30&k0" as the default init string
- load + run the "SYSOP" program
- Choose "C" for Create User Log - this will create a new user log with just 1 record:
  - USER ID: 1
  - USERNAME: SYSOP
  - PASSWORD: SYSOP
- Load and run "BBS". There is no local mode, so you'll have to use another computer to dial into your BBS to test it out.

## The BBS Program
The program itself is in three parts (PRG files):
- BBS : This is a BASIC program that provides the vast majority of the functions of the BBS. This can be modified by the sysop as they see fit.
- BBS.ML : This is the machine language code that provides the I/O functions for the BBS (ie: GETLINE, SHOWFILE, PRINT, etc). Finding a good spot to host this code is the tricky part with the VIC-20. Right now, I have this code sitting in a 3K RAM expansion spot at $0400 (this requires a specific memory expansion cartridge).
- SYSOP : This is a utility program that can do things like create the initial user log, edit users, generate the user list, etc.

## Required SEQ Files
In order to run the BBS, you are going to need some supporting sequential (text) files. These can contain any PETSCII codes you would like. The D64 distribution image contains all of these files that you can then modify with your preferred PETSCII editor (not included with BBS).

You need to create/edit the following SEQ files:
- BANNER : Splash screen showed to user upon initial connect
- MOTD : Message of the Day shown after user logon
- HELP : Main Menu help screen
- NEWUSER1 : Shown to new users prior to filling out application
- NEWUSER2 : Shown to new users after filling out application
- LOGOFF : Shown to users at logoff time
- BANNED : Shown to users who have security level zero (banned)
- TIMEUP : Shown to users when their time has expired
- LOGFULL : Shown to a new user when the user log is full (255 users max)
- INFO : Display information regarding the BBS
- BULLETINS : Listing of available bulletins on the system (numbered 1 to 255)
- SETMENU : Help Menu for the User Settings area
- SYSHELP : Help page for using the system

## Real VIC-20 vs VICE: Differences
The way the BBS answers calls is very simple. The "modem" is programmed to answer after one ring (ats0=1) and all the BBS does is monitor the user port (address 37136) for a carrier tone. On a physical VIC-20, that shows up on bit #4 (ie: AND 8 in BASIC or AND #$08 in ML) whereas in VICE it shows up on bit #5 (ie: AND 16 in BASIC or AND #$10 in ML). Here are the spots in the code it is referenced:
- BBS.ML : at address $0550 and $0552, there is AND #$08 and CMP #$08. That works for the physical VIC. In VICE that is changed to AND #$10 and CMP #$10.
- BBS : line 115 is doing and8 for a real VIC and and16 for VICE
Otherwise, the two images are the same.

## Real Time Clock
The VIC has no way of knowing what the current date is, and it's time keeping ability is hampered by the fact that the internal timer pauses whenver the disk is accessed. This makes using an external real time clock (RTC) chip required to run the BBS effectively. In the BBS BASIC program, there are two subroutines at lines 3400 (get Date) and 3450 (get Time). You will likely need to modify those two subroutines to work with whatever RTC solution you have chosen. In VICE, a DS12C887 is emulated and the BBS-VICE.d64 image contains the code for working with that RTC. My physical VIC uses a custom board made by [JaystoniansRetroShop](https://www.etsy.com/ca/shop/JaystoniansRetroShop) on Etsy that includes a DS12885T RTC chip and 35KB of expansion RAM. So my code at 3400 and 3450 reflects how that chip works. 
