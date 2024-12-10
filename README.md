# Timex Sinclair 1000

[![Check Markdown Links](https://github.com/Andy4495/timex-sinclair-1000/actions/workflows/check-links.yml/badge.svg)](https://github.com/Andy4495/timex-sinclair-1000/actions/workflows/check-links.yml)

Notes on restoring and using a vintage Timex Sinclair 1000 / Sinclair ZX81 personal computer.

## Modifications

### Keyboard Replacement

A common problem with the keyboards is that the plastic membrane used on the cable becomes brittle over the years and essentially disintegrates. So it came as no surprise that this problem affected my unit as well. I removed the old keyboard from the housing and made sure to carefully remove the remnants of the cable from the keyboard connectors KB1 and KB2. It helps to use tweezers and picks to get it all. I replaced it with [this one ordered from eBay][4].

### Video Output

I modified the RF output video signal so that it bypasses the RF Modulator and instead outputs a composite video signal. I used the ["single transistor, 2 resistor"][3] design. The composite video signal produced is not fully compliant, but is good enough to work on my TV with a composite video input. However, my cheap composite-to-HDMI converter box has difficulty with consistently syncing to the signal.

Circuit modifications:

- Although I left it in place, I think it should be possible to remove the existing circuit board inside the RF shield can
- Outside of the shield can, unsolder the middle wire from the "USA" via and move it to the "2" via (this is the video output signal from the ULA chip) ([photo][26])
- Inside the shield can, cut the 5V and video signal wires from the circuit board inside the can (leave enough to solder the transistor legs)
- Remove the channel select switch (near the regulator)
- Solder a jumper between the two switch vias nearest the regulator (this will route 5V to the RF shield can) (see [photo][27])
- Cut the RF signal wire connected to the RCA jack (see [photo][25])
- Disconnect the two parts behind the RCA socket (see [photo][28])
- Install the transistor/resistor modification
  - Collector to 5V wire coming into shield can
  - Solder two resistors to the emitter pin
    - 18 Ohm resistor between the emitter and the RCA output connector
    - 100 Ohm resistor between the emitter and to ground (RF shield can)
  - Base to video input wire
- Post-rework [photo][29], noting the electrical tape under the transistor and on the side of the shield can to insulate against possible shorts

### Regulator

All the other TS1000 and ZX81 restoration articles discuss removing the 7805 voltage regulator and replacing it with a switching DC-DC converter (like at [Traco TSR 1-2450][30]). However, since everyone has a pile of 5V USB wall charger, why not just bypass the regulator circuit and power the board directly with a regulated 5V supply. A 500 mA supply should be sufficient, but I ran across one reference that suggested that the TS1000 needs up to 700 mA.

This requires a few modifications to the PCB:

- Remove inductors I1 and I2
- Remove capacitors C13 and C14 (note that these are radial capacitors that look like resistors and have a light green tint)
- Remove the 7805 voltage regulator and attached heatsink
- Solder jumper wires across the old I1 and I2 vias (i.e., replace I1 and I2 with zero ohm resistors)
- Solder a jumper across the regulator input and output vias, making sure not to connect the middle pin
  - These are the two outer pins on the regulator (the middle pin is ground)

Note that the only [schematic][18] that I could find does not match the circuit on my board. In particular, on my board, I2 is connected between external power ground and PCB ground, which is why the zero ohm jumper across I2 is needed.

Also, if you don't have a 5V charger with the correct plug (a two pin 1/8" audio plug), then you can cut the plug from the original 9V charger from the TS1000 and replace the plug on your 5V charger. Just make sure that the "tip" conductor of the audio plug is positive and the "ring" connector is ground.

In addition, the metal springs inside the power jack were a little loose and didn't make a reliable contact, which caused the TS1000 to power cycle it was bumped. I bent the springs slightly to tighten them. I think these may need to be replaced at some point. If I do replace the power connector, I would probably use either a USB or barrel jack.

### Capacitors

I replaced the two electrolytic capacitors on the board. This was probably not necessary as neither of them were leaking and they both measured the correct capacitance within their rated tolerances. I added some small strips of electrical tape under the new capacitors to keep the leads from shorting to the PCB traces.

### ULA Heat Sink

The Ferranti 2C210E ULA (IC1) appears to be functionning correctly. However, this chip is known to run hot, so I mounted a small IC heatsink on the package.

### RAM Upgrade From 2K to 16K

The TS1000 comes with a 2K RAM chip. I replaced this with a 62256 32K chip, of which 16K is usable due to the way the ULA decodes memory addresses. The 2K RAM chip is not socketed and needs to be unsoldered from the PCB. The replacement chip has a slightly different pin configuration, so some modifications need to be made to route the correct PCB signals to the correct pins. I used [this procedure][19], but soldered the jumper wires to the socket, instead of the 16K RAM chip. Once again, I used some electrical tape to insulate the PCB traces from the modifications.

### Photos

- [Board with video modifications and key component locations][31]
- [Fully modified board][32]

## Unmodified Components

The ROM (IC2) and Z80 CPU (IC3) appear to be fully functional. The ROM contains the "improved" [ROM code version](#rom-versions).

## Next steps

- Use [zxtext2p][7] to convert program to WAV files so that I can load programs from a computer instead needing a cassette player

## Memory Map

| Start |  End | Description | Notes |
| ----: | ---: | ----------- | ------------------------------------------ |
|     0 | 1FFF | BASIC ROM   |                                            |
|  2000 | 3FFF | Shadow ROM  | Disabled with 56K RAM packs                |
|  4000 | 47FF | 2K RAM      | Default internal RAM                       |
|  4000 | 7FFF | 16K RAM     | External RAM pack disables 2K built-in RAM |
|  2000 | FFFF | 56K RAM     | Shadow ROM and internal RAM disabled       |

0 - 8K BASIC ROM  
8-16K ROM Shadow (disabled with 56K RAM pack)  
16-18K RAM 2K (disabled with external RAM packs)  
16-32K RAM 16K (disables internal 2K RAM)  
8-64K 56K RAM pack (diables internal 2K RAM)  

### Memory Size Test

To check the RAM size, enter the following one-line program:

```text
PRINT PEEK 16388+256*PEEK 16389
```

An unmodified TS1000 would print 2048.
Upgrading a RAM chip in the motherboard to 16K would print 32768 (16K ROM + 16K RAM).

## ROM Chip Adapter to Use Standard EPROMS

The ROM in the TS1000 is a mask rom with the pinout of the obsolete 2364 ROM. A more readily available 2764 EPROM can be used with the [23xx Adapter][33] from Retro Innovations. The adapter can be used as-is without any jumpers modified and does not need the 74HCT138 decoder chip.


## ROM Versions

There are multiple versions of the Timex Sinclair ROM. The Timex Sinclair 1000 and ZX81 ROMs are the same.Differences between the machines, e.g. TV frame rate, are performed in software based on a PCB jumper.

| Version/Download Link      | Description                                                              |
| -------------------------- | ------------------------------------------------------------------------ |
| [Standard][10]             | The original, buggy, first version of the ROM as shipped by Sinclair     |
| [Improved][11]             | The debugged second version of the ROM as shipped by Sinclair            |
| [Shoulders of Giants][12]  | Debugged variant by Geoff Wearmouth                                      |
| Other Languages            | Replacement ROMs for other programming languages like Forth and assembly |
| Clones                     | Some clones had slightly modified ROMs                                   |

## References

- [Website][1] with numerous articles and links about the Timex Sinclair 1000 and related personal computers
- ROM [downloads][2]
  - Others downloads not listed in table [above](rom-versions)
    - Other languages: [Forth1][13], [Forth2][14], [Assembly][15]
    - Clones: [Lambda][16], [Power8300][17]
- [Replacement keyboard][4] available on eBay
- Procedures to modify video output to composite video instead of RF output
  - [Single transistor method][3], with photos (this is the method that I used successfully)
- [ZX81 BASIC programs and web emulator][5]
- [Program][6] to converts a text file containing a ZX81 program into a ".p" file suitable for an emulator (this should also work to program a ROM).
  - [Updated version][7] to collapse display file to fit into 1K RAM
- Convert ZX81 program text to a WAV file for loading into machine without cassette: [link][8] or [link][9]
- TS1000 schematic from article in VISTA newsletter [Volume 3, Number 5][18]
- "[Reversible][19]" RAM upgrade procedure
  - Alternate [procedure][20] that cuts traces on the motherboard
  - [Procedure][21] to enable full 32K of on-board RAM
  - Decoding 32K and 56K RAM [procedure][22]
- ULA replacement module: [vLA81][23]
  - Modern FPGA-based plug-in replacement for a failed Ferranti ULA chip ("IC1")
- ROM chip [23xx Adapter][33] from Retro Innovations
- Writeup of another TS1000 [refurbishment][24]
- Photos
  - Composite video circuit inside the [modulator shield can][29]
  - [Entire board][31], showing the video modifications and labels for key components
  - Entire board with [all modifications][32]

## License

The software and other files in this repository are released under what is commonly called the [MIT License][100]. See the file [`LICENSE.txt`][101] in this repository.

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
[33]: http://www.go4retro.com/products/23xx-adapter/
[100]: https://choosealicense.com/licenses/mit/
[101]: ./LICENSE.txt
[//]: # ([200]: https://github.com/Andy4495/timex-sinclair-1000)

[//]: # (This is a way to hack a comment in Markdown. This will not be displayed when rendered.)
