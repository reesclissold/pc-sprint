# PC-SPRINT

The PC-SPRINT is a PC accelerator board design released by Doug Severson in 1985 and published in computer magazines of the time. It is designed to allow overclocking of an [Intel 8088](Datasheets/Intel%208088%20Datasheet.pdf)-based computer such as the IBM 5150 / 5160 PCs, PCjr, Tandy 1000, and I'm sure a whole lot more.

![My PC-SPRINT Installed In My IBM 5150](Retro%20Canada/Images/Mine/pc-sprint-installed.jpg)

In these machines (and others of the time) the system clock is generated by a 14.318MHz crystal connected to an [Intel 8284A](Datasheets/Intel%208284A-1%20Datasheet.pdf) Clock Generator Driver. The 8284A divides the crystal's signal by 3 giving us our 4.77MHz clock. Unfortunately we can't just replace the crystal on the motherboard as this would play havoc with all sorts of things. The basis of this accelerator is to provide an additional, faster crystal coupled with an additional 8284A to generate a faster clock signal that will be used exclusively by the CPU.

The PC-SPRINT also adds a "Turbo" switch as found on later PCs to enable / disable the higher clock rate and a hardware reset button, which is lacking on the IBM 5150. When used with the recommended 22.11MHz crystal, the CPU will run at a clock speed of 7.37MHz giving an impressive 64% processing speed increase. Faster speeds may be possible with faster crystals but are not recommended. Note that in order to utilise this accelerator, the original 4.77MHz 8088 CPU must be replaced with either a faster version of the same CPU (e.g. 8088-2) or equivalent clone (e.g. NEC V20, which is highly recommended as it brings other benefits).

Much more information is available in the [original documentation](PCSPRINT.DOC) ([PDF](PCSPRINT.DOC.PDF)).

## Original File Listing

Here is a rough index of the files included with the package as originally distributed:

- [1STREAD.ME](1STREAD.ME) - Listing of project files (you're reading a modern adaptation of this)
- [ARTWRK1X.BOT](ARTWRK1X.BOT) - Bottom layer printed circuit artwork 1x size (obsolete printer format) ([PDF](ARTWRK1X.BOT.PDF))
- [ARTWRK1X.TOP](ARTWRK1X.TOP) - Top layer printed circuit artwork 1x size (obsolete printer format) ([PDF](ARTWRK1X.TOP.PDF))
- [ARTWRK2X.BOT](ARTWRK2X.BOT) - Bottom layer printed circuit artwork 2x size (obsolete printer format) ([PDF](ARTWRK2X.BOT.PDF))
- [ARTWRK2X.TOP](ARTWRK2X.TOP) - Top layer printed circuit artwork 2x size (obsolete printer format) ([PDF](ARTWRK2X.TOP.PDF))
- [FEEDTHRU](FEEDTHRU) - Top - bottom "feed through" connection diagram ([PDF](FEEDTHRU.PDF))
- [NOPRTYCK.COM](NOPRTYCHK.COM) - Program to disable parity checks
- [PARTLIST](PARTLIST) - Parts list & placement drawing ([PDF](PARTLIST.PDF))
- [PCSPRINT.BAT](PCSPRINT.BAT) - Batch file to print PC-SPRINT info & drawings 
- **[PCSPRINT.DOC](PCSPRINT.DOC) - Description & construction info ([PDF](PCSPRINT.DOC.PDF))**
- [SCHEMATC](SCHEMATC) - Electronic circuit diagram ([PDF](SCHEMATC.PDF))
- [WARMBOOT.COM](WARMBOOT.COM) - Program to set "warm boot" flag

Note that some files have PDF conversions which were included with the version of the package as received by myself, so I have also linked those above.

The author was keen to include the original unmodified package of files with future revisions, so it is available in [pcsprint_1985.zip](References/pcsprint_1985.zip).

## Updates For The 21st Century

### Retro Canada KiCAD Files

![3D Render of Retro Canada's PC-SPRINT](Retro%20Canada/Images/render.png)

A Vintage Computer Federation forum user by the name of Retro Canada [redrew the original PCB design in KiCAD](http://www.vcfed.org/forum/showthread.php?60803-I-overclocked-my-IBM-5150) and [released the files freely](https://sites.google.com/site/tandycocoloco/dropbox/PC-SPRINT.zip) on November 26th 2017.

I have added a mirror of this KiCAD project [here](Retro%20Canada/PC-SPRINT.zip). The package includes gerber files so KiCAD is not required if you are planning on sending the files directly to a PCB fabricator.

![Retro Canada's PC-SPRINT](Retro%20Canada/Images/pc-sprint1.jpg)

The user built their own based on these files (pictured above - [more of their pictures here](Retro%20Canada/Images)) but it should be noted that they had issues with the PC-SPRINT and could only get it to run reliably with a 17.43MHz crystal (CPU clock speed 5.81MHz). It is believed that this is related to DMA activity and I have a proposed solution in development which I have [documented below](#pc-sprint-v2-by-ctrl-alt-rees) - an improved "v2" version of the board which is currently in the prototyping and testing phase.

#### My Own "Retro Canada" Board

![My PC-SPRINT Built Using Retro Canada's Gerber Files](Retro%20Canada/Images/Mine/pc-sprint-pcb.jpg)

I have now successfully built and tested a PC-SPRINT using Retro Canada's files. I used [PCBWay](https://www.pcbway.com/) for my PCB fabrication. I used a 21.47727MHz crystal for a CPU clock speed of 7.16MHz, which coincidentally is the same as the Tandy 1000 EX/HX.

#### My Testing

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

<sup>\* All RAM chips were recently replaced with "New Old Stock" [Samsung KM4164B-15](http://www.minuszerodegrees.net/memory/4164.htm) parts, which have a 150ns access time.</sup>

I have done extensive testing with the above system and had no problems whatsoever. I was expecting issues with DMA activity as others have reported, however have seen none at all. Perhaps this is due to the XT-IDE and its [unusual handling of DMA](https://www.lo-tech.co.uk/xt-cfv3-dma-transfer-mode/). The PC even boots up fine with the turbo mode engaged. Incidentally, cold boot time is 1:02 stock vs. 43 seconds in turbo mode.

The included NOPRTYCHK and WARMBOOT utilities work exactly as advertised with no issues as does the new reset button.

Incidentally, this is how I installed the turbo and reset buttons in the case. Both are 12mm panel mount push buttons - the turbo button is a latching version:

![My PC-SPRINT Reset and Turbo Buttons](Retro%20Canada/Images/Mine/pc-sprint-buttons.jpg)

#### My Benchmarks

I ran through a series of benchmarking applications in both turbo and non-turbo mode. I will sum these up in a table after the screenshots.

#### Landmark

![Landmark Benchmark Before And After Results](Retro%20Canada/Images/Mine/Benchmarks/landmark-before-after.jpg)

|Benchmark|Stock|Turbo|Increase|
|---|---|---|---|
|CPU<sup>*</sup>|3.02MHz|4.60MHz|52.32%|
|FPU|4.56MHz|6.92MHZ|51.75%|
|Video|412.35 chr/ms|446.03 chr/ms|8.17%|

<sup>\* I'm not sure why this figure is different to the "CPU Clock" shown at the top left.

### PC-SPRINT v2 by ctrl-alt-rees

![3D Render of PC-SPRINT v2](PC-SPRINT%20v2/render.png)

First, a quick history lesson.

In 2014 fellow VCFed forum user [Sergey Kiselev](https://github.com/skiselev), author of various high profile 8088 and Z80 hardware projects, analysed the PC-SPRINT and [observed](http://www.vcfed.org/forum/showthread.php?41940-IBM-XT-cpu-upgrade&p=319044#post319044) that it didn't reduce the clock speed when the system was performing DMA activity, which would account for the stability issues. Presumably Retro Canada was not aware of this thread when building their own PC-SPRINT 3 years later. Sergey went on to design and release his own solution, the [Turbo 8088](References/Turbo%208088%20-%20Schematic.pdf), based on his [Xi 8088](http://www.malinov.com/Home/sergeys-projects/xi-8088) ISA processor board.

In 2019, yet another VCFed forum user, inmbolmie, [decided to build one](http://www.vcfed.org/forum/showthread.php?70923-IBM-5160-overclock-Sergey%92s-way), as well as fixing some bugs in Sergey's design. The updated version was dubbed the [Turbo8088 v2](References/turbo8088%20v2.pdf). The board was successfully tested in a 5160 at up to 25MHz giving an 8.33MHz CPU clock speed. Over this speed various problems were reported.

![inmbolmie Sergey Turbo8088 PCB](References/inmbolmie/sergey_prototype_pcb_rotated.jpg)

My problem with the Turbo8088 is that it's a large design that adds a lot of components and complexity. I don't want to tread on Sergey's toes as he is very talented when it comes to electronics and system design, but I decided to see whether it would be possible to build a "PC-SPRINT v2" that incorporates the DMA signals Sergey identified, making for a simple and compact package.

I believe that I have succeeded in this, and my new and improved PC-SPRINT v2 is currently in the prototyping and testing phase.

As all my predecessors have done, I am releasing the PC-SPRINT v2 under the GPL and making the files available here. The package includes the KiCAD and gerber files for PCB fabrication. Once the boards are manufactured and tested I will update this repo with further information.

The PC-SPRINT v2 files are available [here](PC-SPRINT%20v2).

## License

The original  design was released under a license analogous to the modern-day GPL, so I have attached that license here. The PC-SPRINT is therefore assumed to comply with the [Open Hardware Specification](https://en.wikipedia.org/wiki/Open-source_hardware).