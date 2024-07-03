# VIC-BBS
VIC-20 Bulletin Board System

Back in 1983, I got my first computer - a VIC-20. I built a simple BBS program for it and ran it initially on my VIC-1600 VICModem. I had to answer each call manually! Quickly I got a 1650 modem that would auto-answer. I ran that BBS (called the Live Wire BBS) for many years before eventually moving on to running a Commodore 64 BBS (Theatre of Pain BBS) and then in the early 90's, a PC-based BBS (The Danger Zone BBS). I lost the original VIC-BBS diskettes and I've always wanted to re-do it.

Fast forward 40+ years, and I have that opportunity. WiFi modems, memory expanders and SD card readers are all now available for the VIC-20 (and C64/C128) which makes building and running a BBS much easier. This program is the result of about 2 weeks of hard work, preceded by a few years of pondering and experimenting with code.

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
