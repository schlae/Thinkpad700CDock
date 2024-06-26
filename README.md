# Thinkpad 700C Dock Slot Adapter
This set of boards allows you to connect Micro Channel expansion boards to an IBM Thinkpad 700, 700C, 720, or 720C. This is an experimental project based on a lot of my own reverse engineering work, so don't expect it to work perfectly.

The AMP Champ connector appears to be out of production, so this project uses two stacked circuit boards that are slightly thinner (1.2mm) than a normal PC board. The 1.2mm thickness allows the boards, after stacking, to plug right into the docking connector.

## Fabbing and Assembly

Be sure to specify 1.2mm thickness for all the boards!

There are three boards total. The first two are both boards that plug into the lower part of the docking connector. Pick one of them.

* 700CDockSlot - Oriented so a connected Micro Channel card bracket points toward the left edge of the LCD display as viewed from the front. This provides easy access to the component side of the Micro Channel card from behind the laptop.
* 700CDockSlotFlipped - Oriented so a connected Micro Channel expansion card bracket sits at the right edge of the LCD display as viewed from the front. Turning the laptop around, you will see the solder side of an attached Micro Channel expansion card.

The third board plugs into the upper part of the docking connector.

* 700CDockSlotUpper - Provides wiring for IRQ10, IRQ11, IRQ12, IRQ14, and IRQ15.

If you know you won't need those IRQ lines, then you don't need this board.

Once you've soldered up both boards, attach them together using wires connecting the two boards together through J2. Use 1.6mm thick washers or 3D printed spacers in between the boards.

## Configuration

Jumper block J8 lets you select the source of the main 5V rail. Typically you will want to use the 5V position, which is an always-on 5V source. The 5VD position uses the 5V source that shuts off when the laptop is in standby mode. This isn't desirable since the laptop BIOS does not reinitialize and reenable any cards plugged into the adapter.

Test points J9 and J10 are for providing an external source of +12V for cards that require a lot of current (like hard cards or whatever).

## Known Issues

Revision 1 of the design had a few design errors that were fixed in rev 2. In case you have an old revision board, you will need to make the following changes:

* CHRESET should be tied to pin 102 of the docking connector. 
* MADE24 has not been found on the docking connector yet. Most likely this is because the machine never has more than 16M of RAM and it does not support 32-bit cards.

To fix the CHRESET issue, remove U3 and R3. Cut the MADE24 trace at the Micro Channel slot connector (pin A2) and run a wire from pin 102 of the expansion edge connector to U3 pin 2. Connect a 10K resistor from the MADE24 pin of the MCA bus connector (pin A2) to a nearby +5C net (like pin A7).

Signal integrity isn't all that good, but these older 5V logic systems were not known for good signal integrity. The original IBM docking cartridge uses some buffer chips presumably for improving this on some signals, but I don't have access to one nor do I have access to high-resolution photos, so the mystery remains.

Certain types of cards will not work with this setup. Memory cards that use the matched memory extension won't physically fit, and wouldn't work anyway because the 700C doesn't bring out the necessary bus signals. I have not wired up the signals for the Auxiliary Video Extension, so cards supporting this won't work either.

The docking connector brings out a single CD\_SETUP# signal for slot 2, limiting this adapter to a single card slot. In theory it would be possible to support more than one slot; more research is necessary.

# Thinkpad 700C Docking Connector Information
Through a rather tedious manual process, I've been able to reverse engineer the docking connector for this laptop.

| Bottom row (wide end) | Row 2 | Row 3 | Top row (narrow end) |
|-----------------------|-------|-------|----------------------|
| 1: PWR\_SWITCH#         | 61: CD\_SETUP# (slot 2) | 121: +5V              | 181: +5V |
| 2: Unknown, to 39G8600  | 62: GND                 | 122: +10V             | 182: +5V |
| 3: GND                  | 63: AUDIO SUM           | 123: GND              | 183: +5V |
| 4: OSC (14.318MHz)      | 64: GND                 | 124: PS/2 mouse pin 2 | 184: PS/2 mouse pin 1 | 
| 5: GND                  | 65: A23                 | 125: PS/2 mouse pin 6 | 185: PS/2 mouse pin 5 |
| 6: A6                   | 66: A22                 | 126: GND              | 186: GND |
| 7: +5V                  | 67: A21                 | 127: n/c              | 187: GND |
| 8: A5                   | 68: A20                 | 128: n/c              | 188: GND |
| 9: +5V                  | 69: A19                 | 129: n/c              | 189: GND |
| 10: A4                  | 70: A18                 | 130: n/c              | 190: n/c |
| 11: GND                 | 71: A17                 | 131: n/c              | 191: GND |
| 12: A3                  | 72: A16                 | 132: n/c              | 192: GND |
| 13: 5V\_2               | 73: A15                 | 133: n/c              | 193: GND |
| 14: A2                  | 74: A14                 | 134: n/c              | 194: n/c |
| 15: 5V\_2               | 75: A13                 | 135: n/c              | 195: GND |
| 16: A1                  | 76: A12                 | 136: n/c              | 196: GND |
| 17: 5V\_2               | 77: A11                 | 137: n/c              | 197: n/c |
| 18: A0                  | 78: A10                 | 138: n/c              | 198: n/c |
| 19: GND                 | 79: A9                  | 139: n/c              | 199: GND |
| 20: ADL#                | 80: A8                  | 140: n/c              | 200: n/c |
| 21: GND                 | 81: A7                  | 141: n/c              | 201: GND |
| 22: S0 (WR#)            | 82: PREEMPT#            | 142: n/c              | 202: n/c |
| 23: 5V\_2               | 83: BURST#              | 143: n/c              | 203: GND |
| 24: S1 (RD#)            | 84: IRQ9#               | 144: n/c              | 204: n/c |
| 25: 5V\_2               | 85: IRQ3#               | 145: LED5 (floppy)    | 205: LED1 (speaker green) |
| 26: M/IO#               | 86: IRQ4#               | 146: LED6 (HDD)       | 206: LED4 (left arrow) |
| 27: GND                 | 87: IRQ5#               | 147: REFRESH (positive) | 207: GND |
| 28: CMD#                | 88: IRQ6#               | 148: ESYNC# (AVE)     | 208: VSYNC (AVE) |
| 29: GND                 | 89: IRQ7#               | 149: P7 (AVE)         | 209: HSYNC (AVE) |
| 30: ARB/GNT#            | 90: ARB0                | 150: P6 (AVE)         | 210: BLANK (AVE) |
| 31: n/c                 | 91: ARB1                | 151: P5 (AVE)         | 211: GND |
| 32: TC#                 | 92: ARB2                | 152: P4 (AVE)         | 212: DCLK (AVE) |
| 33: GND                 | 93: ARB3                | 153: P3 (AVE)         | 213: GND |
| 34: CHRDY#              | 94: CHCK#               | 154: P2 (AVE)         | 214: GND |
| 35: n/c                 | 95: CHRDYRTN#           | 155: P1 (AVE)         | 215: EDCLK# |
| 36: CD\_SFDBK#          | 96: DB0                 | 156: P0 (AVE)         | 216: EVIDEO# |
| 37: GND                 | 97: DB1                 | 157: Unknown, goes to 39G8600 | 217: GND |
| 38: DB3                 | 98: DB2                 | 158: IRQ11#           | 218: IRQ10# |
| 39: n/c                 | 99: DB4                 | 159: IRQ14#           | 219: IRQ12# |
| 40: DB6                 | 100: DB5                | 160: n/c (pulled up)  | 220: IRQ15# |
| 41: GND                 | 101: DB7                | 161: n/c (pulled up)  | 221: n/c (pulled up) |
| 42: CD\_DS16RTN#        | 102: CHRESET            | 162: n/c              | 222: GND |
| 43: n/c                 | 103: REFRESH#           | 163: Parallel port pin 9 | 223: Parallel port pin 13 |
| 44: +5V                 | 104: DB8                | 164: Parallel port pin 8 | 224: Parallel port pin 12 |
| 45: +5V                 | 105: DB9                | 165: Parallel port pin 7 | 225: Parallel port pin 11 |
| 46: DB11                | 106: DB10               | 166: Parallel port pin 6 | 226: GND |
| 47: GND                 | 107: DB12               | 167: Parallel port pin 5 | 227: Parallel port pin 10 |
| 48: GND                 | 108: DB13               | 168: Parallel port pin 4 | 228: Parallel port pin 17 |
| 49: n/c                 | 109: DB14               | 169: Parallel port pin 3 | 229: Parallel port pin 16 |
| 50: GND                 | 110: DB15               | 170: Parallel port pin 2 | 230: Parallel port pin 15 |
| 51: n/c                 | 111: CD\_DS16#          | 171: Parallel port pin 1 | 231: Parallel port pin 14 |
| 52: n/c                 | 112: SBHE#              | 172: STANDBY# (CN4 64) | 232: GND |
| 53: GND                 | 113: GND                | 173: Serial port pin 3   | 233: Serial port pin 2 |
| 54: +20V AC adapter     | 114: VGA port pin 3     | 174: Serial port pin 1   | 233: Serial port pin 4 |
| 55: +20V AC adapter     | 115: GND                | 175: Serial port pin 6   | 234: Serial port pin 8 |
| 56: +20V AC adapter     | 116: VGA port pin 2     | 176: Serial port pin 7   | 235: Serial port pin 9 |
| 57: +20V AC adapter     | 117: GND                | 177: VGA port pin 4      | 237: VGA port pin 15 |
| 58: +20V AC adapter     | 118: VGA port pin 1     | 178: VGA port pin 11     | 238: VGA port pin 12 |
| 59: +20V AC adapter     | 119: GND                | 179: +10V                | 239: GND |
| 60: +20V AC adapter     | 120: BACKLIGHT\_PWR# (CN4 29) | 180: GND | 240: GND |

Notes:

* 5V\_2 pins are always on. All remaining +5V pins turn off in standby mode.
* STANDBY# is at ~13V when the laptop is off, 5V during normal operation, and 0V during standby.

![Docking connector pin diagram](https://github.com/schlae/Thinkpad700CDock/blob/main/pinout.svg)

**Pin diagram (looking into the back of the laptop)**

The docking connector is in the AMP Champ .050 family, similar to the 5787886-1 but with 240 contacts instead of 200. I've had success connecting to the docking port using two stacked PCBs with 0.050" edge fingers. The PCBs need to be 1.2mm thick instead of the normal 1.6mm.

The docking port breaks out several ports on the laptop, for convenience:
* Parallel port
* Serial port
* PS/2 mouse port
* VGA port
* AC adapter

There also also all the signals required to implement a single Micro Channel expansion slot, including the Auxiliary Video Extension (AVE). This does not include the +12V and -12V rails. It also does not appear to break out the CHRESET signal.

Several pins implement functions specific to this laptop, including:
* PWR\_SWITCH#: Bring this line low to turn on the laptop. Connected in parallel with the physical power switch.
* LED1, LED4, LED5, LED6: Presumably a docking station could duplicate these LEDs (or perhaps control them?)
* SOFT\_POWEROFF#: Tied to the power board's connector pin 64.
* BACKLIGHT\_PWR#: Tied to the power board's connector pin 29.

# Thinkpad 700C Hard Disk Connector

The pinout for the hard disk drive is as follows:

| Pin | Function | Pin | Function |
|-----|----------|-----|----------|
| 1 | CHRESET#?  | 2 | CHRESET |
| 3 | SBHE#      | 4 | +5V |
| 5 | (key)      | 6 | S0 (WR#) |
| 7 | GND        | 8 | S1 (RD#) |
| 9 | CHRDY#     | 10 | GND |
| 11 | CMD# (buffered) | 12 | A3 |
| 13 | A2        | 14 | A1 |
| 15 | M/IO#     | 16 | A0 |
| 17 | ARB/GNT#  | 18 | TC# |
| 19 | IRQ14#    | 20 | DB13 |
| 21 | GND       | 22 | DB11 |
| 23 | DB10      | 24 | DB7 |
| 25 | ARB3      | 26 | ARB2 |
| 27 | GND       | 28 | ARB1 |
| 29 | ARB0      | 30 | BURST# |
| 31 | PREEMPT#  | 32 | DB6 |
| 33 | GND       | 34 | DB5 |
| 35 | DB2       | 36 | +5V |
| 37 | DB0       | 38 | DB15 |
| 39 | GND       | 40 | DB14 |
| 41 | DB12      | 42 | DB9 |
| 43 | GND       | 44 | DB8 |
| 45 | DB4       | 46 | +5V |
| 47 | DB3       | 48 | DB1 |
| 49 | GND       | 50 | CD\_DS16# (buffered) |
| 51 | CD\_SETUP# (slot 1) | 52 | ADDRESS\_SEL# |
| 53 | n/c | 54 | n/c |
| 55 | n/c | 56 | n/c |
| 57 | n/c | 58 | n/c |
| 59 | n/c | 60 | n/c |

Note: CHRESET# is inverted from the usual MCA CHRESET signal. It goes low to reset the drive.

The hard drive connector goes through a flex connection to a 52-pin 2mm header on the actual hard drive itself. The pinout matches 1:1. Pins 53-60 are not wired through. The motherboard connector is probably from the AMP Champ .050 FH family: 5176381-2. Possible matching connectors include the AMP 5176376-2 and the 5176379-2.

The hard drive is of the DBA (direct bus attach) ESDI type, and shows up as slot 1. (Slot 2 is on the docking connector.)

The ADDRESS\_SEL# output goes low when the address range assigned to the hard drive shows up on the address bus: 0x351x). It does not go low during card setup mode (CD\_SETUP# pulled low), which seems to only use the 4 address bits to decode the POS registers.




