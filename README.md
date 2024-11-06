# Timex Sinclair 1000

[![Check Markdown Links](https://github.com/Andy4495/timex-sinclair-1000/actions/workflows/check-links.yml/badge.svg)](https://github.com/Andy4495/timex-sinclair-1000/actions/workflows/check-links.yml)

This repo contains notes on restoring and using a vintage Timex Sinclair 1000 / Sinclair ZX81 personal computer.

## Inventory

I purchased three TS1000 computers from eBay in 2024:

### Unit #1

- S/N:
- Listing noted that it booted, but keyboard not working. No returns.
- Included the "improved" [ROM version](#rom-versions) as described below
- Replaced [keyboard][4]
- Modified video from RF to composite output, using ["single transistor, 2 resistor"][3] design
- With new keyboard and video mod, tested working on the 2006 TV
  - Could not get RF modulator output to work. It is possible that the video cable that came with the unit is faulty.
  - Test additional units with a new (known good) video cable

#### Next steps

- Test out EPROM mod board with 2764 EPROM
- Replace 5V regulator and Aluminum heat sink with switching regulator (no heat sink needed)
- See if I can add more RAM directly to board
- See if I can program a new EPROM with a game program already loaded, may need a modification to EPROM to jump to a specific location with RUN
  - This may require more hardware mods, since the ULA generates separate ROMCS and RAMCS signals based on address
- Original 1K RAM can be expanded to 16K, 32K, or 56K

### Unit #2

- S/N:
- Listed as untested, but description says it is "in good working order".

### Unit #3

- S/N: F-040804
- Listed as untested, but in excellent cosmetic condition. Sold as-is.

## Memory Map

0 - 8K BASIC ROM  
8-16K ROM Shadow (disabled with 56K RAM pack)  
16-17K RAM 1K (disabled with external RAM packs)  
16-32K RAM 16K (disables internal 1K RAM)  
8-64K 56K RAM pack (diables internal 1K RAM)  

## On-board RAM upgrade

- <http://www.zx81.de/english/32k-rame.htm>
- <http://blog.tynemouthsoftware.co.uk/2017/10/zx81-internal-16k-ram-reversible-version.html> (16K RAM, reversible procedure)
- <http://blog.tynemouthsoftware.co.uk/2014/07/zx81-internal-16k-ram.html> (same as above, but cuts traces and is not easily "reversible")

## ROM Chip Pinout and Adapter to Use Standard EPROMS

Writeup how to do a 2764 EPROM modification, both signals and module height.

- No jumpers/mods needed with bare-bones go4retro board
<http://www.go4retro.com/products/23xx-adapter/>

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
- [ROM Downloads][2]
  - Version 1 "Standard ROM" [download link][10]
  - Version 2 "Improved ROM" [download link][11]
  - "Shoulders of Giants" variant by Geoff Wearmouth [download link][12]
- [Replacement keyboard][4] available on eBay
- Procedures to modify video output to composite video instead of RF output
  - [Single transistor method][3], with photos (this is the method that I used successfully)
- [ZX81 BASIC programs and web emulator][5]
- [Program][6] to converts a text file containing a ZX81 program into a ".p" file suitable for an emulator (this should also work to program a ROM).
  - [Updated version][7] to collapse display file to fit into 1K RAM
- Convert ZX81 program text to a WAV file for loading into machine without cassette: [link][8] or [link][9]

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
[100]: https://choosealicense.com/licenses/mit/
[101]: ./LICENSE.txt
[//]: # ([200]: https://github.com/Andy4495/timex-sinclair-1000)

[//]: # (This is a way to hack a comment in Markdown. This will not be displayed when rendered.)
