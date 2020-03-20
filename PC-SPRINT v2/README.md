# PC-SPRINT v2

![PC-SPRINT v2 Prototype Installed](Images/pc-sprint-v2-installed.jpg)

Building on the work of Doug Severson from 1985, the PC-SPRINT v2 is an updated and improved DIY Open Hardware overclocking device for 8088-based systems. Please see the [PC-SPRINT README](/README.md) for an introduction to the original device.

With the potential for DMA-related problems I decided that it would be worth attempting to improve the design of the PC-SPRINT. Although I haven't run into these problems myself it will be a fun project and an opportunity to teach myself some things about KiCAD, PCB fabrication and early PCs. With all that in mind, the PC-SPRINT v2 will aim to seamlessly switch back to the stock clock speed whenever there is DMA activity, hopefully eliminating the possibility of lockups.

![3D Render of PC-SPRINT v2](Images/render.png)

To understand why and how, we'll start with a quick history lesson.

In 2014 fellow VCFed forum user [Sergey Kiselev](https://github.com/skiselev), author of various high profile 8088 and Z80 hardware projects, analysed the PC-SPRINT and [observed](http://www.vcfed.org/forum/showthread.php?41940-IBM-XT-cpu-upgrade&p=319044#post319044) that it didn't disable the turbo mode when the system was performing DMA activity, as per other "turbo" PC clones. [DMA or "Direct Memory Access"](https://en.wikipedia.org/wiki/Direct_memory_access) is a way for devices to read and write directly to RAM, bypassing the CPU. If the device in question isn't designed to take account of the CPU running at a different clock speed to the rest of the system this can result in potential problems. Based on this, Sergey went on to design and release his own solution, the [Turbo 8088](References/Turbo%208088%20-%20Schematic.pdf), based on his [Xi 8088](http://www.malinov.com/Home/sergeys-projects/xi-8088) ISA processor board.

In 2019, yet another VCFed forum user, inmbolmie, [decided to build one](http://www.vcfed.org/forum/showthread.php?70923-IBM-5160-overclock-Sergey%92s-way), as well as fixing some bugs in Sergey's design. The updated version was dubbed the [Turbo8088 v2](../References/turbo8088%20v2.pdf). The board was successfully tested in a 5160 at up to 25MHz giving an 8.33MHz CPU clock speed. Over this speed various problems were reported.

![inmbolmie Sergey Turbo8088 PCB](../References/inmbolmie/sergey_prototype_pcb_rotated.jpg)

As you can see, the Turbo8088 is a large design that adds a lot of components and complexity. I don't want to tread on Sergey's toes as he's one of the best when it comes to systems architecture and electronics but I decided to see whether it would be possible to incorporate the DMA signals Sergey identified into the PC-SPRINT, making for a simple and compact hybrid solution.

I believe that I have succeeded in this, and my PC-SPRINT v2 prototypes are currently being tested.

As this started life as a freely available project it only seemed right to release the PC-SPRINT v2 under the GPL. The package includes the KiCAD and gerber files for PCB fabrication. This is an ongoing project and this repo will be regularly updated as new information comes to light from ongoing research and testing.

The PC-SPRINT v2 files are available [here](KiCAD).

## Bill Of Materials (BOM) / Component Values

The bill of materials is mostly the same as the original PC-SPRINT. Additional resistors and diodes are the same values as the originals. The only new component is an NPN transistor. I'm using a C2120 but just about any small signal NPN transistor should be fine as it's used as a NOT gate and therefore driven to saturation (unbiased).

|Component|Type|Value|
|---|---|---|
|R1,R2|Resistor|560Ω 0.25W|
|R3,R9|Resistor|3.3KΩ 0.25W|
|R4,R5,R6,R7,R8|Resistor|10KΩ 0.25W|
|C1,C3,C4|Capacitor (Tantalum)|10μF|
|C2|Capacitor (Ceramic Disc)|.01μF (10nF)|
|D1,D2,D3,D4,D5,D6|Diode|1N4148|
|U1|Pin Header|Straight 9 Pin x2 (Bottom of PCB)|
|U2,U3|DIP Socket|18 Pin<sup>\*</sup>|
|X1|HC49S Crystal Oscillator|22.11MHz<sup>\*\*</sup>|
|Q1|NPN Transistor|C2120|
|J1|Pin Header|90 Degree 4 Pin|
|J2|Pin Header|90 Degree 2 Pin|

<sup>\* One of these will be populated with a new 8284A IC. The other will house your existing 8284A as removed from the motherboard.</sup>

<sup>\*\* 22.11MHz was the original recommended value and has been tested. For more info on timing crystal speeds see the [Timing Crystal Values Tested](#timing-crystal-values-tested).</sup>

### Additional / External Parts To Consider:

* Switches - 1x momentary (reset) and 1x latching (turbo), type depending on preferred installation method.
* Interconnecting cables - 4 conductor ribbon / 2 conductor.
* DuPont connectors - 4-pin / 2-pin.

## DMA Signals on the 5150 Motherboard

In my early research I came to the conclusion that these signals wouldn't be available on the 5150, but after a lot more reading and consulting the [IBM Technical Reference](References/IBM_5150_Technical_Reference_6322507_APR84.pdf), I believe that we can actually pick them up from very similar places to Sergey's design for the 5160. The Turbo8088 and indeed the PC-SPRINT v2 would then be able to incorporate the following:

- HRQDMA: From pin 10 of the 8237 DMA controller IC. This pin goes HIGH on DMA activity.
- HRQWAIT / DMAWAIT: From pin 7 of an 74LS175 "flip flop" IC labeled U88 on the XT, however I am yet to identify its location on the 5150. The most likely candidate is U26 (next to the DIP switches) but this needs to be confirmed before we hook anything up to it. As above, this pin also goes HIGH on DMA activity.

These inputs are connected to an OR gate and then an inverter. When combined with an AND gate on the turbo switch, this should give us the required logic to switch to the lower clock speed if either of these inputs go HIGH. In the spirit of the original design I have implemented this logic using discrete components, but it could be easily adapted to use [7400 series TTL](https://en.wikipedia.org/wiki/List_of_7400-series_integrated_circuits) instead if you're so inclined.

![Old vs. New PC-SPRINT Turbo Switching Logic](Images/pc-sprint-v2-new-dma-logic.jpg)

Original circuit on the left, new additions on the right.

## Current Status

Here are the specs of my IBM 5150 as tested:

|Part|Model|Notes|
|---|---|---|
|CPU|[NEC V20](https://en.wikipedia.org/wiki/NEC_V20)|4.77MHz stock / 7.16MHz "turbo" with PC-SPRINT|
|FPU|[Intel 8087-2](https://en.wikipedia.org/wiki/Intel_8087)|8087 co-processor rated up to 8MHz|
|RAM|640KB|(256KB onboard, 384KB on SixPakPlus card)<sup>*</sup>|
|Motherboard|64-256KB [Later Revision](http://www.minuszerodegrees.net/5150/motherboard/5150_motherboard_revisions.htm)|[10/27/82 BIOS](http://minuszerodegrees.net/5150/bios/5150_bios_revisions.htm)|
|HDD|XT-IDE with 512MB CompactFlash card|Running [IDE_XTP.BIN BIOS](https://www.lo-tech.co.uk/wiki/XTIDE_Universal_BIOS)|
|Graphics|IBM CGA||
|Network|3com EtherLink II||
|Floppy|2x Tandon 360KB 5.25"|Stock IBM Floppy ISA interface|
|PSU|Standard 110VAC / 63.5W|
|Operating System|[IBM PC DOS 2000](https://winworldpc.com/product/pc-dos/2000)||

<sup>\* All RAM chips were recently replaced with "New Old Stock" [Samsung KM4164B-15](http://www.minuszerodegrees.net/memory/4164.htm) parts, which have a 150ns access time.</sup>

The PCBs have arrived from [PCBWay](https://www.pcbway.com/) and the v2 is currently undergoing testing. The new design is around 20mm longer than the original, meaning that it now extends underneath the left hand floppy / hard drive (if fitted). Thankfully there is plenty of clearance, however it is recommended that the board is assembled with 90 degree pin headers on J1 and J2 (as pictured below) as there is minimal space for the cables to protrude upwards. The PCB design may be modified in future but this is not a current priority.

![Assembled Prototype PC-SPRINT v2](Images/pc-sprint-v2-assembled.jpg)

**The PC-SPRINT v2 is working as intended but without the J2 DMA pins connected. This means that it is permanently in turbo mode and may cause problems with DMA transfers.**

The next step will be to get these connected up and tested, however this will entail an intense oscilloscope session and may involve removal of the motherboard so I am waiting until I have time to schedule this in properly.

For now, based on my experience, the PC-SPRINT (v1 or v2) is perfectly safe to use with a 22.11MHz crystal.

## Timing Crystal Values Tested

These are the values I have tested in my system so far. Your results may differ.

|Crystal Value|CPU Clock Speed|Result|Notes|
|---|---|---|---|
|21.4772MHz|7.16MHz|Working|Same clock speed as [Tandy 1000 EX and HX](https://en.wikipedia.org/wiki/Tandy_1000#Tandy_1000_EX_and_HX).|
|22.1184MHz|7.37MHz|Working|Testing ongoing but all good so far. This is the original PC-SPRINT recommended value.|
|24MHz|8MHz|System will not boot / locks up when enabled|Same clock speed as later [IBM PC/AT](https://en.wikipedia.org/wiki/IBM_Personal_Computer/AT).|

For more information on timing please see the [original PC-SPRINT documentation](/blob/master/README.md) for now.

## License

The original  design was released under a license similar to the modern-day GPL (IANAL), so I have attached that license here. The PC-SPRINT v2 is therefore assumed to comply with the [Open Hardware Specification](https://en.wikipedia.org/wiki/Open-source_hardware).