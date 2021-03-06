# Vortex POK3R (RGB) Custom Firmware Project

*Disclaimer: This project comes with no warranty, and may be used for free at your own risk. I do
this in my free time for fun, as such there are no guarantees of progress or success.*

### Goals / Progress
- POK3R
    - Extract unencrypted POK3R firmware and flash contents. **DONE**
    - Reverse engineer the POK3R firmware and update procedure. **DONE**
    - Application to write new firmware to POK3R over USB. **DONE**
- POK3R RGB
    - Extract unencrypted POK3R RGB firmware and flash contents. **DONE**
    - Reverse engineer the POK3R RGB firmware and update procedure. **DONE**
    - Application to write new firmware to POK3R RGB over USB. **DONE**
- Vortex CORE
    - Extract unencrypted CORE firmware and flash contents. *decrypted firmware*
    - Reverse engineer the CORE firmware and update procedure. *same as pok3r rgb*
    - Application to write new firmware to CORE over USB. *untested*
- New Firmware
    - Open-source firmware compatible with POK3R and POK3R RGB. *WIP*
        - Patch OpenOCD to allow JTAG flashing of HT32. **DONE**
        - Firmware can be installed from Vortex firmware over USB (no hardware required). **DONE**
        - Firmware can be rolled back with Votex firmware update application. *TDB*

## pok3rtool
The `pok3rtool` CLI application runs on Linux, Windows, and Mac OS X. Along with some development
tools, `pok3rtool` implements the firmware update protocol over USB for the POK3R and POK3R RGB.

The `pok3rtool` executable is built with CMake. You will need the LibChaos submodule cloned to
build it. After it is compiled, it should have no runtime dependencies, and can be run portably.

**WARNING: THIS TOOL CAN VERY EASILY BRICK YOUR KEYBOARD IF USED INCORRECTLY, MAKING IT
UNUSABLE WITHOUT EXPENSIVE DEVELOPMENT TOOLS. READ THE DOCUMENTATION, POSSIBLY READ THE
CODE, AND PROCEED AT YOUR OWN RISK.**

[See the wiki for pok3rtool usage, warnings, etc.](https://github.com/ChaoticConundrum/pok3r_re_firmware/wiki/pok3rtool-Usage)

### POK3R Firmware Patch
A very small firmware patch will allow the contents of the flash storage to be read out over USB,
with the protocol used to read the version number during an update. This is effectively
accomplished by changing a single byte in the Vortex firmware updater. In the v117 updater,
changing the byte `0x09` at `0x1A7E5E` to `0x07` will patch the firmware update to allow reading
flash. This circumvents a test in the code that normally only allows reading between 0x2800 and
0x2C00. The relevant disassembly for this is at 0x7240 of the v117 firmware. See patch_v117.txt.

A patched version of the firmware is provided, see `firmware_v117_patched.bin`. This can be
loaded with `pok3rtool flash`.

### POK3R RGB Firmware Patch
The POK3R RGB update protocol is more minimal, and inherently doesn't allow reading arbitrary
offsets. The equivalent patch for the POK3R RGB adds the ability to read from arbitrary addresses
to the update protocol. See patch_v130.txt

A patched version of the firmware is provided, see `firmware_v130_patched.bin`. This can be
loaded with `pok3rtool flash`.

### Vortex CORE Firmware Patch
A patched version of the firmware is provided, see `firmware_v104_patched.bin`. This can be
loaded with `pok3rtool flash`.

## References
[SpritesMods hacked a Cooler Master Quickfire Rapid-i](https://spritesmods.com/?art=rapidisnake),
which uses a similar Holtek ARM microcontroller. I also think the firmware was written for that
keyboard by the same people as the POK3R. His blog gave me some ideas when working on the POK3R,
and I ended up coming to mostly the same conclusions as he did.
