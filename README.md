# Retro computing on the Ulx3s ECP5 FPGA board

## Hardware vs. Software emulation

Emulation of old computers, game consoles and arcade machines on [Field-Programmable Gate Arrays](https://en.wikipedia.org/wiki/Field-programmable_gate_array) (FPGAs), if done well, can be much more accurate than software emulation, as it can recreate the digital logic of the original computer. This enables it to achieve much more accurate timing and minimal lag. It can also be used to preserve, for posterity, the hardware logic of those original machines.

Not all FPGA recreations are this faithful to the original and do not need to be to run the software from these early machines. Cycle-accurate CPU implementations execute all instructions in exactly the same number of machine cycles as the original CPU. This is important to accurately run software from some machines, such as the Atari 2600 games console, where the timing of the code had to closely match the timing of the video signal. That is because the Atari 2600 used a technique known as "Racing the Beam" to produce video output using a minimal amount of RAM and no video frame buffer, or equivalent. See the book also called [Racing the Beam](https://www.amazon.co.uk/Racing-Beam-Computer-Platform-Studies/dp/0262539764).

It is possible for CPU implementations to be more than just cycle accurate: they can be accurate right down to the logic level, implementing the same flip-flops and exactly the same combinatorial logic. The same can be true of other for other chips, not just the CPU. However, this level of accuracy is seldom needed unless the intention is to preserve the digital logic of the original chip.

Avoiding lag between input and output devices can be important when playing fast-moving games, for them to have the same "feel" as the original. Lag is usually measured in video frames, i.e. how many video frames are displayed before the effect of user input is seen. It is hard to avoid lag with software emulation. With hardware emulation buffering of the video signal beyond what was done in the original machine needs to be avoided, and to achieve this CRT monitors need to be used as LCD monitors always have some lag. 

## Using FPGAs

To recreate the digital logic of a machine using an FPGA, you need to code the logic in a Hardware Description Language (HDL). The two main hardware description languages are Verilog (and its more powerful SystemVerilog variant), and VHDL. However, you do not have to write directly in these languages, as you can use more powerful languages that generate HDL, such as [migen](https://m-labs.hk/gateware/migen/), [nmigen](https://github.com/nmigen/nmigen), [SpinalHDL](https://github.com/SpinalHDL/SpinalHDL), Chisel and [Silice](https://github.com/sylefeb/Silice).

Hardware Description Languages were originally designed to simulate digital logic and they can still be used for that purpose using open source tools such as iVerilog and Verilator. Nowadays however, there are also synthesis tools that convert the logic into bitstream files that can be uploaded to an FPGA and effectively turn the FPGA into a machine that executes the digital logic described by the HDL. The same HDL can also be used to create hard-wired chips such as [ASIC](https://en.wikipedia.org/wiki/Application-specific_integrated_circuit)s.

At the lowest level FPGAs do not match the exact transistors and logic gates of the original machine, but instead use look-up tables (LUTs) as their logic elements. LUTs contain a number of flip-flops and a configurable logic function that maps a number of inputs to a number of outputs. By wiring together a large number of LUTs, the original logic can be recreated.

## Open Source FPGAs

FPGA boards can be open source in a number of ways. The board itself can be open source hardware, and the tools that convert the HDL to a bitstream can be open source software. The only thing that cannot currently be open source is the FPGA chip itself.

Until fairly recently, all software that converted HDL to the bistream for an FPGA was proprietary commercial software owned, licensed and sold by the FPGA chip vendor.

But starting from 2015, open source tool chains have been available to synthesize the HDL and produce FPGA bitstreams.

The main open source tools for FPGAs are [Yosys](https://github.com/YosysHQ/yosys) for synthesis, [nextpnr](https://github.com/YosysHQ/nextpnr) for place-and-route and [GHDL](https://github.com/ghdl/ghdl) and its [Yosys plugin](https://github.com/ghdl/ghdl-yosys-plugin) to support VHDL.

The main synthesis tools for Verilog are [iVerilog](https://github.com/steveicarus/iverilog) and [Verilator](https://github.com/verilator/verilator).

Each chip supported by the open source tool chain has its own reverse engineering project that includes chip-specific tools. For the ECP5 board, this is [Project Trellis](https://github.com/YosysHQ/prjtrellis).

There are nightly builds of all these tools at the [Open Tool Forge](https://github.com/open-tool-forge/fpga-toolchain) project.

For The Ulx3s board, the main tool for uploading bitstreams is [fujprog](https://github.com/kost/fujprog).

## The Mist and Mister projects

The [Mist](https://github.com/mist-devel/mist-board/wiki) and [Mister](https://github.com/MiSTer-devel/Main_MiSTer/wiki) projects have recreated the most old computers. The Mister project is under active development and currently has recreations of more than 40 old computers, nearly 20 games consoles, 80 or more arcade machines and a few other machines.

But the only part of these projects that is open source is the HDL. The FPGA development boards are proprietary, as is the synthesis software.

## Retro computing on the Ulx3s

The [Ulx3s board](https://github.com/emard/ulx3s) is open source hardware with all its design files available on github. The board was designed using the open source PCB design tool, Kicad. It uses the Lattice ECP5 chip.

There are many other open source boards based on Lattice chips, but the Ulx3s is particularly suitable for retro computing because of its I/O capability. It has video output that is compatible with HDMI devices, it has USB connections to the FPGA, and it has Pmod-compatible headers that allow other devices to be attached.

It also has buttons that can emulate joystick controls, and an OLED/LCD connector which is useful for diagnostics.

In addition, it has an ESP32 co-processor that usually [runs micropython](https://github.com/emard/esp32ecp5). The ESP32 has Wifi and can be used to upload bitstreams to the FPGA and to do 2-way communication with the FPGA.

Both the FPGA and the ESP32 have access to an SD card reader, so the FPGA can directly access an SD card, or the ESP32 can act as a file server for the FPGA. The latter is very useful for retro computing. Files on an SD card in on the ESP32 flash memory can be accessed from the FPGA using an on-screen display (OSD), which overlays a file browser on the HDMI (or VGA) output and allows the user to choose files, such as game roms to load.

The Mister FPGA has a similar and currently slightly more powerful OSD.

## Verilog or VHDL

Early versions of the open source toolchain only supported Verilog, and Verilog is still better supported than VHDL. But there is now an open source GHDL plugin for the Yosys open source synthesis tool that support VHDL.

Not all VHDL is supported however, and Yosys has better support for Verilog than SystemVerilog.

## Porting Mister projects

Porting Mister projects to the Ulx3s and other open source boards can be difficult, as VHDL and SystemVerilog often has to be manually converted to something that Yosys supports, although there are some tools that can help with this conversion.

The communication with the ESP32 is also a lot different to the way that the Mister system communicates with its Arm coprocessor running Linux, and that also requires some conversion. Also the way HDMI output is generated is different.

Some, but not all, of the retro-computing projects for the Ulx3s are Mist or Mister ports, but some are new projects and some use components from a variety of sources.

## The History of computing

Most of the machines recreated on the Ulx3s (and with the Mister project) date from the mid 70s to the mid 90s. Machines before that time did not tend to play games, and machines later than that are too complex to be recreated on cheap FPGAs.

### Early machines

Stored program computing using digital logic arguably started with the [Manchester Baby](https://en.wikipedia.org/wiki/Manchester_Baby) in 1948, and was followed the next year by the Cambridge [EDSAC](https://en.wikipedia.org/wiki/EDSAC) machine. There are creations of the [Manchester Baby](https://forum.mystorm.uk/t/manchester-baby-emulation/450) and [EDSAC](https://forum.mystorm.uk/t/tft-touch-screen/289) on open source Blackice boards, and the Mister project includes a recreation of [EDSAC](https://github.com/MiSTer-devel/EDSAC_MiSTer).

### The 1950s

The 1950s saw the creation of many machines based on vacuum tubes and the emergence of IBM as the major player. There are no current open source recreations of these machines.

### The 1960s

The 1960s saw the emergence of mainframe and mid-range computers using transistors and of mini-computers using TTL logic. IBM's most successful range of machines was the System/360. There is a [VHDL implementation of the System 360 Model 50](https://github.com/ibm2030/IBM2030), but this is not yet on open source hardware. 

The dominant mini-computer supplier was Digital Equipment Corporation (DEC). Their first machine was the PDP-1, and the Mister project includes a [recreation of this](https://github.com/MiSTer-devel/PDP1_MiSTer).

The most popular mini-computer was the PDP-11 and there are recreations of this. For example, the Mister project includes a recreation of the Russian [BK0011M](https://github.com/MiSTer-devel/BK0011M_MiSTer) machine, which uses a clone of the PDP-11 CPU.

In 1969, development the Unix operating system was started, with the first releases in the early 70s running on a PDP-11.

### The 1970s

The first games consoles appeared in the early to mid 1970s, and mainly implemented variants of [Pong](https://en.wikipedia.org/wiki/Pong). Many of these used the [AY-3-8500](https://en.wikipedia.org/wiki/AY-3-8500) chip. The Mister project includes a [recreation of this chip](https://github.com/MiSTer-devel/AY-3-8500-MiSTer) and there is the [start of a port](https://github.com/lawrie/ulx3s_ay_3_8500) of this to the Ulx3s board. It is written in Verilog.

The first microprocessor was the Intel 4004 4-bit chip, and this was soon followed by the 8-bit 8008, and the 8080.

The CP/M operating system for the Intel 8080 appeared in 1974. The Ulx3s board has an [implementation of this](https://github.com/lawrie/ulx3s_cpm_z80) for a Z80 chip.

The first home computer kits were based on the 8080, the most famous successful being the [Altair 8800](https://en.wikipedia.org/wiki/Altair_8800) which appeared in January 1975. There is a recreation of the [Altair 8800](https://github.com/MiSTer-devel/Altair8800_Mister) including an emulation ot its front panel in the Mister project. There is a simpler version of this without the front panel on the Ulx3s board. The [Ulx3s version](https://github.com/ulx3s/fpga-odysseus/tree/master/tutorials/07-Computer) is entirely in Verilog.

The next year, 1976, Steve Jobs and Steve Wozniak formed Apple Computer, and produced the [Apple 1](https://en.wikipedia.org/wiki/Apple_I) kit computer. There is a version of the [Apple 1](https://github.com/lawrie/apple-one), written in Verilog, on several open source boards including the Ulx3s.

Another 1976 kit computer was the [Cosmac Elf](https://en.wikipedia.org/wiki/COSMAC_ELF). This uses the RCA 1802 chip. There is a [SpinalHDL implementation of this](https://github.com/lawrie/FPGACosmacELF) on the Ulx3s board.

1977 was the year when the first fully assembled home computers appeared: the [Apple 2](https://en.wikipedia.org/wiki/Apple_II), the [Commodore Pet](https://en.wikipedia.org/wiki/Commodore_PET) and the [TRS 80 Model 1](https://en.wikipedia.org/wiki/TRS-80). There is a Verilog implementation of the [TRS 80](https://github.com/lawrie/ulx3s_trs_80) on the Ulx3s board, which includes an OSD from which games and other software can be run. There is also an implementation of the [Apple 2](https://github.com/emard/apple2fpga) on the Ulx3s board, written in Verilog and VHDL, which currently only compiles using the Lattice Diamond proprietary software.

The Mister project includes all three of these computers, but there is currently no implementation of the Commodore Pet on the Ulx3s.

Also in 1976, the most successful of the second generation of games consoles was produced: the [Atari 2600](https://en.wikipedia.org/wiki/Atari_2600). The Mister project has an implementation of this, and there is an [open source version](https://github.com/lawrie/atari_2600) on the TinyFPGA BX board, but not yet on the Ulx3s board.

In 1979, Atari launched their range of [8-bit home computers](https://en.wikipedia.org/wiki/Atari_8-bit_family). The Mister project includes an implementation of these, but not the Ulx3s.

The first of the Texas Instruments home computers based on the [TMS9900](https://en.wikipedia.org/wiki/Texas_Instruments_TMS9900) chip, the TI-99/4, also appeared in 1979.

That year also saw the [Mattel Intellivision](https://en.wikipedia.org/wiki/Intellivision) games console, which was a rival to the Atari 2600, and used the General Instruments CP1610 chip, which was based on the PDP-11. There is an unofficial Mister version of this.

Unix was ported to other machines in the 1970s and 1980s and there is a version of the [Cortex](https://gitlab.com/pnru/cortex) project on the Ulx3s board that implements a version of V6 Unix on a TMS9900 chip. This is a recreation of the [PowerTran Cortex](http://www.powertrancortex.com/) machine that appeared in electronics today in 1982 and 1983. The original machine ran several operating systems, but not Unix.

The late 1970s also saw the start of the microprocessor based arcade machines.

### The 1980s

The 1980s saw an explosion of home computers, game consoles and arcade machines.

In 1980, the [Commodore VIC-20](https://en.wikipedia.org/wiki/Commodore_VIC-20), the [Acorn Atom,](https://en.wikipedia.org/wiki/Acorn_Atom) the [Tandy Color Computer](https://en.wikipedia.org/wiki/TRS-80_Color_Computer) (Coco), and the [Sinclair ZX80](https://en.wikipedia.org/wiki/ZX80) were introduced.

The Mister project implements all of these. The Ulx3s board has an implementation of the [VIC-20](https://github.com/lawrie/ulx3s_vic_20) that uses an OSD to run software. It has an implementation of the [Acorn Atom](https://github.com/lawrie/ulx3s_acorn_atom) that runs software from an SD card, and the start of a [ZX80](https://github.com/lawrie/ulx3s_zx81) implementation.

The [Video Genie](https://en.wikipedia.org/wiki/Video_Genie) system, called the Dick Smith System 80 in Australia, was introduced in 1980 and is practically a clone of the TRS 80 Model 1. The Ulx3s TRS 80 implementation supports this variant.

1981 saw the successor of the Acorn Atom: the [BBC Micro](https://en.wikipedia.org/wiki/BBC_Micro). The Ulx3s version of the [BBC micro](https://github.com/lawrie/ulx3s_bbc_micro) runs software from an SD card, but only has VGA, not HDMI output.

1981 also saw the emergence of the [TI-99/4A](https://en.wikipedia.org/wiki/Texas_Instruments_TI-99/4A), and the [ZX 81](https://en.wikipedia.org/wiki/ZX81). The Ulx3s board has an excellent implementation of the [TI-99/4A](https://github.com/Speccery/icy99) that includes an OSD, and is described in this [video](https://www.youtube.com/watch?v=zdST3wz00KU). There is also an implemention of the ill-fated [TI-99/2](https://gitlab.com/pnru/ti99) computer with its Hexbus interface, on the Ulx3s.

MSDOS and the IBM PC were introduced in 1981. The Ulx3s board has the [Next186](https://github.com/emard/Next186) impolementation of MSDOS and FreeDOS, that came be used to play MSDOS games. Mister has the more powerful [ao486](https://github.com/MiSTer-devel/ao486_MiSTer). Here is a [video](https://www.youtube.com/watch?v=K-dl4bQy3yw&t=29s) of some DOS games on the Ulx3s.

The [Commodore 64](https://en.wikipedia.org/wiki/Commodore_64) (C64) came out in 1982 and was the most successful home computer ever with over 17 million sold. It had the biggest games library of any computer. The Ulx3s board has an implementation of the [C64](https://github.com/emard/ulx3s_c64) that is written in VHDL and Verilog and compiled using the GHDL Yosys plugin. It has an OSD and sound via the famous [SID chip](https://en.wikipedia.org/wiki/MOS_Technology_6581).

1982 was also the year of the [Jupiter Ace](https://en.wikipedia.org/wiki/Jupiter_Ace) home computer that ran Forth rather than Basic, and the [Sinclair ZX Spectrum](https://en.wikipedia.org/wiki/ZX_Spectrum). There is a version of the [Jupiter Ace](https://github.com/lawrie/jupiter_ace) on the Ulx3s board, but it does not support software loading. There is a version of the [Speccy](https://github.com/lawrie/ulx3s_zx_spectrum), that includes an OSD and software loading.

More second generation games consoles also came out in 1982, including the [ColecoVision](https://en.wikipedia.org/wiki/ColecoVision), which has an [implementation on the Ulx3s board](https://github.com/lawrie/ulx3s_colecovision) with software loading via an OSD. The practically identical [Sega SG 1000](https://en.wikipedia.org/wiki/SG-1000) games console came out the next year, and is [again on the Ulx3s board](https://github.com/lawrie/ulx3s_sg_1000).

The Croatian [Galaksija](https://en.wikipedia.org/wiki/Galaksija_(computer)) kit computer came out in 1983, and is [implemented on the Ulx3s board](https://github.com/emard/galaksija).

1983 saw the introduction of the [MSX standard](https://en.wikipedia.org/wiki/MSX). MSX machines were mainly produced by the Japanese. There is an [MSX implementation](https://github.com/lawrie/ulx3s_msx) with software loading via the OSD on the Ulx3s board.

Nintendo introduced the Famicom games console in 1983, and it was renamed to the [Nintendo Entertainment System](https://en.wikipedia.org/wiki/Nintendo_Entertainment_System) (NES) in North America and came out in 1985. There is an [excellent implementation of the NES](https://github.com/emard/nes_ecp5) on the Ulx3s, which includes an OSD and supports most cartridges using a large range of mappers. There is a [video](https://www.youtube.com/watch?v=EQ97hIMpufQ) of some NES games on the Ulx3s.

In 1984, Apple introduced the [Macintosh 128K](https://en.wikipedia.org/wiki/Macintosh_128K) using a [Motorola 68000](https://en.wikipedia.org/wiki/Motorola_68000) chip, followed by the 512K and the [Mac Plus](https://en.wikipedia.org/wiki/Macintosh_Plus) in the following years. There is an implementation of the [Mac Plus](https://github.com/lawrie/ulx3s_mac128) on the Ulx3s board, which has PS/2 mouse support and emulates floppy disks using an OSD.

The end of 1984 saw the introduction of the [Sinclair QL](https://en.wikipedia.org/wiki/Sinclair_QL), which used a 68008 chip. The Ulx3s has a [version of the QL](https://github.com/lawrie/ulx3s_ql) emulating microdrives using an OSD.

In 1985, more machines based on the 68000 chip came out, including the [Atari ST](https://en.wikipedia.org/wiki/Atari_ST) and the [Amiga](https://en.wikipedia.org/wiki/Amiga). The MIST project name is formed from the MI of Amiga and ST, of the Atari ST, and both Mist and Mister have implementation of those.

The Ulx3s board has a version off the [Minimig Amiga implementation](https://github.com/emard/Minimig_ECS) that needs to be built using Lattice Diamond. It has an OSD but accesses the SD card directly, rather than using the ESP32. Here is a [video](https://www.youtube.com/watch?v=q0nysMydf4I) of some Amiga games on the Ulx3s.

The Croatian [Orao](https://en.wikipedia.org/wiki/Orao_(computer)) computer was produced in 1985, and is [implemented on the Ulx3s board](https://github.com/emard/UK101onFPGA), with an OSD.

The [Sega Master System](https://en.wikipedia.org/wiki/Master_System) games console was released in 1985 in Japan and in the following years in the rest of the world. There is the [start of an implementation](https://github.com/lawrie/ulx3s_sms) of this on the Ulx3s board.

In the late 1980s the first version of Niklaus Wirth's [Oberon operating system](https://en.wikipedia.org/wiki/Oberon_(operating_system)) was produced, which was a development system for his [Oberon programming language](https://en.wikipedia.org/wiki/Oberon_(programming_language)). In 2013 , he invented a RISC chip to run this OS, and there is a [version of this on the Ulx3s board](https://github.com/emard/oberon) that supports an SD card and PS/2 keyboard and mouse.

Nintendo had produced the Game & Watch portable games console in 1980, but the first very successful portable games console was the [Nintendo Game Boy](https://en.wikipedia.org/wiki/Game_Boy), which came out in 1989. There is the start of [Game Boy implementation](https://github.com/lawrie/slabboy), written in SpinalHDL, on the Ulx3s board.

There are not currently many Arcade machine implementations on the Ulx3s board, but there is a version of [Phoenix](https://github.com/emard/vhdl_phoenix).

### The 1990s

In 1990, Sega introduced the [Game Gear](https://en.wikipedia.org/wiki/Game_Gear) portable games console, which was practically identical to the Sega Master System.

In the 1990s, 16 and 32-bit games consoles were released. The Super Famicom was released in Japan in 1990 and as the [Super Nintendo Entertainment System](https://en.wikipedia.org/wiki/Super_Nintendo_Entertainment_System) (SNES) in 1991 in North America. The Mister implementation of SNES, which is largely in VHDL has been [ported to the Ulx3s board](https://github.com/emard/SNES_MiSTer_ulx3s), using the GHDL Yosys plugin, and runs some, but not all, SNES games.

Other fourth generation gamers consoles such as the Sega Genesis/MegaDrive, the Turbografx 16 and the NeoGeo are implemented by Mister but not yet on the Ulx3s board.

Fifth generation games consoles use 3D Graphical Processing Units (GPUs). The Playstation is being implementated on an FPGA, but it is unlikely that the Ulx3s or even the Mister's De10 Nano board is powerful enough to run it.

The Linux OS was introduced in 1991 and has been in continuual development since.

### The 2010s

In 2010 the [Risc-V](https://en.wikipedia.org/wiki/RISC-V) Instruction Set architecture (ISA) was introduced and has been in continual development since.

Risc-V is very popular on open source FPGAs and there are many implementations of the 32-bit version of it on the Ulx3s.

There are two implementation of Risc-V Linux on the Ulx3s board: [LiteX VexRiscv](https://github.com/litex-hub/linux-on-litex-vexriscv) written in migen and SpinalHDL, and [SaxonSoc](https://github.com/SpinalHDL/SaxonSoc/tree/dev-0.1/bsp/radiona/ulx3s/smp), written entirely in SpinalHDL. Here is a [video](https://www.youtube.com/watch?v=ch_RTDHCYmo&t=417s) of SaxonSoc SMP Linux on the Ulx3s board.

Several games for the Ulx3s boards have been written in C and run on a Risc-V SoC, such as [Super Miyamoto Sprint](https://github.com/dan-rodrigues/super-miyamoto-sprint).

## Missing from the Ulx3s board

Significant 8-bit computers missing from the Ulx3s board and the Atari 8-bit computers, the Tandy Color Computer and the Amstrad CPC.

A significant missing 16-bit computer is the Atari ST.

Most of the 16-bit games consoles, several portable games consoles, including the later Gameboy versions, such as the Gameboy Advance, and lots of arcade machines are still to do.

The Mister project has about 5 Russian computers of which the BK0011M is probably the most significant.

It has a couple of Japanese computers of which the Sharp X68000 is probably the most significant.

It also has more UK computers such as the Oric, Dragon 32/64 (Tandy Coco clone), the Amstrad PCW,the Sam Coupe (ZX Spectrum compatible successor), and the Acorn Archimedes, which used an Arm processor. Other UK home computers included the Lynx, the Enterprise 64/128, the Grundy Newbrain and the Memotech MDX.

## Pre-built bitstreams

There are lots of pre-built bitstreams for the Ulx3s retro computers mentioned above in the [ulx3s_bitstreams](https://github.com/lawrie/ulx3s_bit_streams) repository.

Here is a [video](https://www.youtube.com/watch?v=YE7pSuZiN9Y&t=6s) of some of the games on the Ulx3s board.

