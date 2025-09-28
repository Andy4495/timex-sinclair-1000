# Timex Sinclair 1000

[![Check Markdown Links](https://github.com/Andy4495/timex-sinclair-1000/actions/workflows/check-links.yml/badge.svg)](https://github.com/Andy4495/timex-sinclair-1000/actions/workflows/check-links.yml)

Restoring and using a vintage Timex Sinclair 1000 personal computer. This should also generally apply to a Sinclair ZX81.

## Modifications

### Keyboard Replacement

The keyboard cable to the PCB through connectors KB1 and KB2 had become brittle and partially disintegrated, rendering the keyboard unusable. This is a common problem with the Sinclair membrane keyboards, so much so that replacements are readily available on [eBay][4].

All the remnants from the old cable need to be removed from connectors KB1 and KB2 before installing the new keyboard. Tweezers, picks, and a magnifying lens are helpful tools to get all the bits out.

### Video Output

I modified the RF output video signal so that it bypasses the RF Modulator and instead outputs a composite video signal. I used the ["single transistor, 2 resistor"][3] design. After some experimentation, I settled on a value of 82 Ohms for the emitter-video-out resistor. The composite video signal produced with these changes works on my TV with a composite video input, a cheap [video-to-VGA][vga] converter, and an [RGBtoHDMI with Analog][rgb2hdmi] board.

[vga]: https://www.amazon.com/dp/B075N462CL
[rgb2hdmi]: https://retrohackshack.com/product/rgbtohdmi/

#### Circuit modifications

- Remove the channel select switch (near the voltage regulator)
- Solder a jumper between the two switch vias nearest the regulator to route 5V to the RF shield can (see [photo][27])
- Outside of the RF shield can, unsolder the middle wire from the "USA" via and move it to the "2" via (this is the video output signal from the ULA chip) ([photo][26])
- Remove the top metal piece of the RF shield can
- Inside the shield can, cut the 5V and video signal wires from the circuit board inside the can, leaving enough wire to solder the transistor legs
- Cut the RF signal wire connected to the RCA jack (see [photo][25])
- Disconnect the two parts behind the RCA socket (see [photo][28])
- Install the transistor/resistor modification. I used a general-purpose [2N3904 transistor][36]:
  - Collector pin to 5V wire coming into shield can
  - Solder two resistors to the emitter pin
    - 82 Ohm resistor between the emitter and the RCA output connector
    - 100 Ohm resistor between the emitter and to ground (RF shield can)
  - Base pin to video input wire
- Post-rework [photo][29]; note the electrical tape under the transistor and on the side of the shield can to insulate against possible shorts

#### Video Quality

With the above modifications and resistor values, the video output signal measured about 1.0 V max with the video-to-VGA converter (75 Ohm load) and 1.5 V max with the RGBtoHDMI (500+ Ohm load).

The [RGBtoHDMI][41] gave a very sharp image with the `Sinclair/ZX81 Composite` profile, after running `Auto Calibrate Video Sampling` and then manually updating two `Sampling Menu` settings:

- `DAC A Y Hi`: 058 (0.75 V)
  - Values from 0.66 to 0.84 V were stable, so I chose the middle
- `DAC F Y V Sync`: 004 (0.05 V)
  - Value 003 (0.04 V) was also stable

### Regulator

All the other TS1000 and ZX81 restoration articles discuss removing the 7805 voltage regulator and replacing it with a switching DC-DC converter (like a [Traco TSR 1-2450][30]). However, since everyone has a pile of regulated 5V USB wall chargers, why not just bypass the regulator circuit and power the board directly with a regulated 5V supply? The main disadvantage to this approach is that 9 V is no longer available on the edge connector; that pin will supply 5 V instead. I am not sure if there are any cartridges that make use of the 9 V power. A 500 mA supply should be sufficient, but I ran across one reference that suggested that the TS1000 needs up to 700 mA.

This requires a few modifications to the PCB:

- Remove inductors I1 and I2
- Remove capacitors C13 and C14 (note that these are radial capacitors that look like resistors, but have a light green tint)
- Remove the 7805 voltage regulator and attached heatsink
- Solder jumper wires across the old I1 and I2 vias (i.e., replace I1 and I2 with zero ohm resistors)
- Solder a jumper across the regulator input and output vias, making sure not to connect the middle pin
  - These are the two outer pins on the regulator (the middle pin is ground)

Note that the only TS1000-specific [schematic][18] that I could find does not match the circuit on my board. In particular, on my board, I2 is connected between external power ground and PCB ground, which is why the zero ohm jumper across I2 is needed for this modification. I created a hand-drawn version of what I think is the [actual power input section][35].

Also, I did not have a 5V charger with the correct plug (a two pin 1/8" audio plug), so I ended up cutting the plug from the original TS1000 9V charger and replaced the plug on my 5V charger. I was extra careful and double-checked that the "tip" conductor of the audio plug was positive and the "ring" connector was ground before attempting to power the computer with the modified charger.

In addition, the metal contact springs inside the power jack were a little loose and didn't make a reliable contact with the plug. This caused the TS1000 to power cycle if it was bumped or moved. I bent the springs slightly to tighten them. I think these may need to be replaced at some point. If I do replace the power connector, I would probably use either a USB or barrel jack.

### Capacitors

I replaced the two electrolytic capacitors (C3 and C5) on the board. This was probably not necessary as neither were leaking and both measured the correct capacitance within their rated tolerances. I added some small strips of electrical tape under the new capacitors to keep the leads from shorting to the PCB traces.

- C3 is rated at 22 uF and 16 V and is connected between the 9V rail and ground near the edge connector. This capacitor is not included on the [VISTA schematic][18]. With the regulator modifications mentioned [above](#regulator), this effectively puts this capacitor across the 5V rail and ground on my board.
- C5 is rated at 1 uF and 50 V and is connected between the Z80 reset pin and ground. The [VISTA schematic][18] notes this as a 5 V capacitor, but the one I removed was marked as 50 V.

### ULA Heat Sink

The Ferranti 2C210E ULA (IC1) appears to be functionning correctly. However, this chip is known to run hot, so I mounted a small IC heatsink on the package.

### RAM Upgrade From 2K to 16K

The TS1000 comes with a 2K RAM chip. I replaced this with a 62256 32K chip, of which 16K is usable due to the way the ULA decodes memory addresses. The 2K RAM chip is not socketed and needs to be unsoldered from the PCB. The replacement chip has a slightly different pin configuration, so some modifications need to be made to route the correct PCB signals to the correct pins.

I used [this procedure][19] by Tynemouth Software, but soldered the jumper wires to the socket instead of the new RAM chip. This leaves the RAM chip intact. Once again, I used some electrical tape to insulate the PCB traces from the modifications.

#### Details on How the RAM Upgrade Works

The RAM in my TS1000 was a Toshiba part [TMM2016P-1][40] (24-pin, 2Kx8, 100ns). I replaced it with a 62256 RAM (28-pin, 32Kx8, 100ns). Fortunately, the TS1000 PCB footprint has space for a 28-pin chip.

The signal locations are mostly matching between the two parts if the "bottom" of the two chips are aligned (i.e., line up 2016 pin 12 with 62256 pin 14). The table below lists the changes needed for the signals that don't match.

A TS100 should be configured from the factory with a jumper wire placed on the PCB location "LK2" and location "LK1" should be empty. This jumper configuration routes the A10 signal to the RAM chip.

To simplify the modification, some address signals are swapped. This only impacts where the data is stored internal to the chip. It does not impact actual operation becase the same address location is used for both reads and writes. The physical jumper wires for this modification are connected to the cathode side of several diodes for ease of connection, as some of the signals are not otherwise easily accessible on the PCB.

| 2016 Pin | RAM Footprint Signal | 62256 Pin | 62256 Signal | Notes                                         |
| :------: | :------------------: | :-------: | :----------: | :-------------------------------------------- |
| N/A      | RFSH                 | 1         | A14          | Bend up socket pin. Connect to Diode D1 (A11) |
| N/A      | Vcc                  | 2         | A12          | See [Note](#note-on-62256-pin-2) below  |
| 1        | A7                   | 3         | A7           |                                               |
| 2        | A6                   | 4         | A6           |                                               |
| 3        | A5                   | 5         | A5           |                                               |
| 4        | A4                   | 6         | A4           |                                               |
| 5        | A3                   | 7         | A3           |                                               |
| 6        | A2                   | 8         | A2           |                                               |
| 7        | A1                   | 9         | A1           |                                               |
| 8        | A0                   | 10        | A0           |                                               |
| 9        | D0                   | 11        | D0           |                                               |
| 10       | D1                   | 12        | D1           |                                               |
| 11       | D2                   | 13        | D2           |                                               |
| 12       | Vss/GND              | 14        | Vss/GND      |                                               |
| 13       | D3                   | 15        | D3           |                                               |
| 14       | D4                   | 16        | D4           |                                               |
| 15       | D5                   | 17        | D5           |                                               |
| 16       | D6                   | 18        | D6           |                                               |
| 17       | D7                   | 19        | D7           |                                               |
| 18       | /CS                  | 20        | /CE          |                                               |
| 19       | A10                  | 21        | A10          |                                               |
| 20       | /OE                  | 22        | /OE          |                                               |
| 21       | /WE                  | 23        | A11          | Bend up socket pin. Connect to Diode D3 (A12) |
| 22       | A9                   | 24        | A9           |                                               |
| 23       | A8                   | 25        | A8           |                                               |
| 24       | Vcc                  | 26        | A13          | Bend up socket pin. Connect to Diode D5 (A13) |
| N/A      | /WE                  | 27        | /WE          |                                               |
| N/A      | Vcc                  | 28        | Vcc          |                                               |

##### Note on 62256 Pin 2

Since the TS1000 can only address 16K of RAM without additional logic, one of the 62256 address lines needs to be tied either high or low. Fortunately, the existing 28-pin RAM layout on the PCB routes 5V to pin 2 of the 28-pin RAM footprint. So A12 is tied high without needing to make any physical board modifications. This effectively uses one less address bit, and therefore only 16K of the 32K chip's address space will be accessed.

### Photos

- [Board with video modifications and key component locations][31]
- [Fully modified board][32]

## Unmodified Components

The ROM (IC2) and Z80 CPU (IC3) appear to be fully functional. The ROM contains the "improved" [ROM code version](#rom-versions).

## Memory Map

| Start |  End | Description | Notes |
| ----: | ---: | ----------- | ------------------------------------------ |
|     0 | 1FFF | BASIC ROM   |                                            |
|  2000 | 3FFF | Shadow ROM  | Disabled with 56K RAM packs                |
|  4000 | 47FF | 2K RAM      | Default internal RAM                       |
|  4000 | 7FFF | 16K RAM     | External RAM pack disables 2K built-in RAM |
|  2000 | FFFF | 56K RAM     | Shadow ROM and internal RAM disabled       |

### Memory Size Test

To check the RAM size, enter the following one-line program:

```text
PRINT PEEK 16388+256*PEEK 16389
```

An unmodified TS1000 would print 2048.
Upgrading a RAM chip in the motherboard to 16K would print 32768 (16K ROM + 16K RAM).

## ROM Chip Adapter to Use Standard EPROMS

The TS1000 uses a mask ROM (Motorola part ZCM38818P) which has the pinout of the obsolete [2364 ROM][38]. A more readily available 2764 EPROM can be used with the [23xx Adapter][33] from Retro Innovations. The bare-PCB version of the adapter can be used as-is with the default jumper settings and does not need the 74HCT138 decoder chip.

### Mapping a 2764 EPROM to the TS1000 2364 ROM

The signal connections from a 2764 to 2364 are listed in the table below. Note that the 2364 has 24 pins and the 2764 has 28 pins. The pins are mostly the same if the bottom of the 2364 is aligned with the bottom of the 2764 (i.e., line up 2364 pin 12 with 2764 pin 14).

| 2364 Pin | 2364 Signal | 2764 Pin | 2764 Signal | Notes                         |
| :------: | :---------: | :------: | :---------: | :---------------------------- |
| N/A      | N/A         | 1        | Vpp         | Pull high (connect to Vcc)    |
| N/A      | N/A         | 2        | A12         | 2764 pin 2 -> 2364 pin 21     |
| 1        | A7          | 3        | A7          |                               |
| 2        | A6          | 4        | A6          |                               |
| 3        | A5          | 5        | A5          |                               |
| 4        | A4          | 6        | A4          |                               |
| 5        | A3          | 7        | A3          |                               |
| 6        | A2          | 8        | A2          |                               |
| 7        | A1          | 9        | A1          |                               |
| 8        | A0          | 10       | A0          |                               |
| 9        | D0          | 11       | D0          |                               |
| 10       | D1          | 12       | D1          |                               |
| 11       | D2          | 13       | D2          |                               |
| 12       | Vss/GND     | 14       | Vss/GND     |                               |
| 13       | D3          | 15       | D3          |                               |
| 14       | D4          | 16       | D4          |                               |
| 15       | D5          | 17       | D5          |                               |
| 16       | D6          | 18       | D6          |                               |
| 17       | D7          | 19       | D7          |                               |
| 18       | A11         | 20       | /CE         | Pull low (connect to Vss/GND) |
| 19       | A10         | 21       | A10         |                               |
| 20       | /CE         | 22       | /OE         |                               |
| 21       | A12         | 23       | A11         | 2764 pin 23 -> 2364 pin 18    |
| 22       | A9          | 24       | A9          |                               |
| 23       | A8          | 25       | A8          |                               |
| 24       | Vcc         | 26       | N/C         | Pull high (connect to Vcc)    |
| N/A      | N/A         | 27       | /PGM        | Pull high (connect to Vcc)    |
| N/A      | N/A         | 28       | Vcc         | 2764 pin 28 -> 2364 pin 24    |

## ROM Versions

There are multiple versions of the Timex Sinclair ROM. The Timex Sinclair 1000 and ZX81 ROMs are the same. Differences between the machines, e.g. TV frame rate, are performed in software based on a PCB jumper.

| Version/Download Link      | Description                                                              |
| -------------------------- | ------------------------------------------------------------------------ |
| [Standard][10]             | The original, buggy, first version of the ROM as shipped by Sinclair     |
| [Improved][11]             | The debugged second version of the ROM as shipped by Sinclair            |
| [Shoulders of Giants][12]  | Debugged variant by Geoff Wearmouth                                      |
| Other Languages            | Replacement ROMs for other programming languages like Forth and assembly |
| Clones                     | Some clones had slightly modified ROMs                                   |

## Next steps

- Use [zxtext2p][7] to convert program to WAV files so that I can load programs from a computer instead needing a cassette player

## References

- [Website][1] with numerous articles and links about the Timex Sinclair 1000 and related personal computers
- ROM [downloads][2]
  - Others downloads not listed in table [above](#rom-versions)
    - Other languages: [Forth1][13], [Forth2][14], [Assembly][15]
    - Clones: [Lambda][16], [Power8300][17]
- [Replacement keyboard][4] available on eBay
- Procedures to modify video output to composite video instead of RF output
  - [Single transistor method][3], with photos (this is the method that I used successfully)
- RGBtoHDMI video converter [wiki][41]
- [ZX81 BASIC programs and web emulator][5]
- [Program][6] to convert a text file containing a ZX81 program into a ".p" file suitable for an emulator (this should also work to program a ROM).
  - [Updated version][7] to collapse display file to fit into 1K RAM
- Convert ZX81 program text to a WAV file for loading into machine without cassette: [tapeutils][8] or [zx81putil][9]
- TS1000 schematic from article in VISTA newsletter [Volume 3, Number 5][18]
  - Corrected [power input/regulator section][35]
- "[Reversible][19]" RAM upgrade procedure
  - Alternate [procedure][20] that cuts traces on the motherboard
  - [Procedure][21] to enable full 32K of on-board RAM
  - Decoding 32K and 56K RAM [procedure][22]
- ULA replacement module: [vLA81][23]
  - Modern FPGA-based plug-in replacement for a failed Ferranti ULA chip ("IC1")
- ROM chip [23xx Adapter][33] from Retro Innovations
  - 23xx Adapter [product information page][37]
  - 2364 [pinout][38] and [datasheet][39]
- Article on [replacing a 2364 ROM with 2764 EPROM][34]
- TMM2016 RAM [datasheet][40]
- Writeup of another TS1000 [refurbishment][24]
- Photos
  - Composite video circuit inside the [modulator shield can][29]
  - [Entire board][31], showing the video modifications and labels for key components
  - Entire board with [all modifications][32]

## License

The software and other files in this repository are released under what is commonly called the [MIT License][100]. See the file [`LICENSE.txt`][101] in this repository.

All hardware modifications mentioned here are for informational purposes only, and are a summary of changes made to my particular hardware, which may be different from yours. You should do your own research before making modifications to your hardware.

[1]: https://www.timexsinclair.com/computers/timex-sinclair-1000/
[2]: https://k1.spdns.de/Vintage/Sinclair/80/Sinclair%20ZX81/ROMs/
[3]: https://www.bytedelight.com/?page_id=3560
[4]: https://www.ebay.com/itm/304989279583
[5]: http://tom-e-white.com/zx81-not-only-30-programs/web/index.html
[6]: https://freestuff.grok.co.uk/zxtext2p/index.html
[7]: https://github.com/tomwhite/zxtext2p?tab=readme-ov-file
[8]: http://www.zx81stuff.org.uk/zx81/tapeutils/overview.html
[9]: https://github.com/mcleod-ideafix/zx81putil
[10]: https://k1.spdns.de/Vintage/Sinclair/80/Sinclair%20ZX81/ROMs/zx81%20version%201%20'standard'%20rom%20(Sinclair).rom
[11]: https://k1.spdns.de/Vintage/Sinclair/80/Sinclair%20ZX81/ROMs/zx81%20version%202%20'improved'%20rom%20(Sinclair).rom
[12]: https://k1.spdns.de/Vintage/Sinclair/80/Sinclair%20ZX81/ROMs/zx81%20'shoulders%20of%20giants'.rom
[13]: https://k1.spdns.de/Vintage/Sinclair/80/Sinclair%20ZX81/ROMs/h4th%20(Forth).rom
[14]: https://k1.spdns.de/Vintage/Sinclair/80/Sinclair%20ZX81/ROMs/tree4th%20(Forth).rom
[15]: https://k1.spdns.de/Vintage/Sinclair/80/Sinclair%20ZX81/ROMs/aszmic%20(z80%20assembler%20os).rom
[16]: https://k1.spdns.de/Vintage/Sinclair/80/Sinclair%20ZX81/ROMs/Lambda%20(org)%20(zx81%20clone).rom
[17]: https://k1.spdns.de/Vintage/Sinclair/80/Sinclair%20ZX81/ROMs/Power8300%20(org)%20(zx81%20clone).rom
[18]: https://archive.org/details/vista-newsletter/VISTA%20Newsletter%20v3%20n5/page/n5/mode/2up
[19]: http://blog.tynemouthsoftware.co.uk/2017/10/zx81-internal-16k-ram-reversible-version.html
[20]: http://blog.tynemouthsoftware.co.uk/2014/07/zx81-internal-16k-ram.html
[21]: http://www.zx81.de/english/32k-rame.htm
[22]: https://web.archive.org/web/20100121145742/http://www.user.dccnet.com:80/wrigter/index_files/Thanks%20for%20all%20the%20Memories.htm
[23]: https://www.vretrodesign.com/products/vla81-zx81-ula-replacement
[24]: https://www.leadedsolder.com/2021/11/30/timex-sinclair-1000-keyboard-ula-fix.html
[25]: https://www.bytedelight.com/wp-content/uploads/2018/10/IMG_20181030_130956.jpg
[26]: https://www.bytedelight.com/wp-content/uploads/2018/10/IMG_20181030_123817.jpg
[27]: https://www.bytedelight.com/wp-content/uploads/2018/10/IMG_20181030_125303.jpg
[28]: https://www.bytedelight.com/wp-content/uploads/2018/10/IMG_20181030_131008.jpg
[29]: ./extras/pics/TS1000-video-mod.jpeg
[30]: https://www.mouser.com/datasheet/2/687/tsr1_datasheet-3049675.pdf
[31]: ./extras/pics/TS1000-board-with-video-mods-.jpeg
[32]: ./extras/pics/TS1000-after-mods.jpeg
[33]: https://store.go4retro.com/23xx-adapter/
[34]: https://wereallgeeks.wordpress.com/2024/05/22/replace-a-dead-2364-masked-rom-with-a-2764-eprom-of-the-same-size/
[35]: ./extras/pics/TS1000-pwr-input-sch.jpeg
[36]: https://en.wikipedia.org/wiki/2N3904
[37]: http://www.go4retro.com/products/23xx-adapter/
[38]: https://myoldcomputer.nl/technical-info/datasheets/integrated-chip-pinout-and-datasheets/memory/2364-2/
[39]: https://myoldcomputer.nl/Files/Datasheet/2364-Commodore.pdf
[40]: https://tvsat.com.pl/PDF/M/MCM2018_mot.pdf
[41]: https://github.com/hoglet67/RGBtoHDMI/wiki
[100]: https://choosealicense.com/licenses/mit/
[101]: ./LICENSE.txt
[//]: # ([200]: https://github.com/Andy4495/timex-sinclair-1000)

[//]: # (This is a way to hack a comment in Markdown. This will not be displayed when rendered.)
