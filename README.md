# Famicom Titler schematics

This is my attempt at capturing the schematics for the PCBs of the
[SHARP Famicom Titler][], using [KiCad][] 8.

This is primarily based on some photos that [Zorchenhimer][] showed on
his stream (since I don't have access to the hardware), with some
guesswork for parts that were hidden (e.g. by the silkscreen) or
otherwise unclear. His work on reverse engineering the device has also
helped with figuring out or confirming some aspects.

[SHARP Famicom Titler]: https://en.wikipedia.org/wiki/Famicom_Titler
[KiCad]: https://www.kicad.org/
[Zorchenhimer]: https://www.twitch.tv/zorchenhimer

## Overview

The Titler is made up of 6 PCBs; 4 internal and 2 in the hardwired
external controllers (joypads).

This project is mainly concerned with the internal PCBs, as the joypads
are, as far as I know, identical to the regular Famicom's joypads
electrically, including the way they are different (one having a mic).

The four internal PCBs are, briefly:

- [x] The keyboard
- [x] The I/O board (input/output; handles controllers etc)
- [ ] The A/V board (audio/video; overlaying Famicom onto the input etc)
- [ ] The Famicom board (main system: CPU, PPU, cartridge, etc)

Except for the Famicom board, these are all single-layer PCBs.

## Keyboard

The left half of the Titler has a set of 16 keys (or buttons), plus a
switch for turning on and off the input video (for the overlay).

These are all mounted on one PCB, along with some passives and a single
chip (a TC4021BP shift register), and an 8-pin connector.

This board is connected to the I/O board via a similar connector there.

The mode switch is wired directly to the connector and GND, being left
open (unconnected) when in the other position.

Electrically, the other keys are arranged in a 2x8 matrix. The 8-key
groups are selectable via the connector, while the pairs are wired to
the shift register, and also have pull-ups.

When connected to the I/O board, and character entry is selected, the
currently active 8-key group is read in exactly the same way as a
regular controller 1 on the expansion port (it replaces D1 on $4016).

With the groups labelled as A and B, and the number giving the bit of
the shift register, the physical layout of the keys is like this:

```
A2 B2 B3 A3 B7
A1 B1 A4 B4 B7
A0 B0 A5 B5 A6 B6
```

## I/O board

This board handles most of the user I/O, either directly or via
connectors; connecting it all back to the main Famicom board.

It has a total of 7 connectors, two of which go to the main board, one
goes to the keyboard, two go to the joypads, one is the externally
accessible Famicom expansion connector, and one goes to the touchpad.

It can be divided into three mostly independent sections:
- joypads and expansion connector
- keyboard and touchpad
- selection of which data to send back to the main board

The selection section consists of two TC74HC157P mux chips and a BJT,
all controlled by a signal coming in via a the main board connector,
that I believe ultimately comes from a switch on the side of the Titler
(on the A/V board), labelled (in Japanese) "character entry" (on/off).

The joypads section is mostly filtering and protection components
(ferrite beads, resistors, etc.), around the 3 connectors, and also
has pull-ups for some of the signals, and pi filters for their power.

The keyboard and touchpad section is the most complicated, using 4 BJTs
and 4 different ICs, one of which I haven't been able to find a
datasheet for (or really any other reference to). Based on how it's used
I'm guessing it's an ADC. This section also generates its own clock (for
the ADC chip), using two of the inverters in one of the other chips.

## A/V board

I have not captured this board yet, but I believe it contains most of
the analog parts of the audio and video circuitry; including the
[genlock][] and overlay, conversion to/from S-Video and composite, etc.

It also has the power input, and a few control knobs that are externally
accessible on the side of the Titler.

[genlock]: https://en.wikipedia.org/wiki/Genlock "Generator locking"

## Famicom board

I have not captured this board yet, but I believe it primarily contains
the main Famicom system (CPU, PPU, etc), and both the cartridge port and
the built-in "cartridge" that is used when no cartridge is inserted.

It also appears to be a multi-layer board (at least two), and is fairly
tightly packed with components, including some bodges on the back. This
may make it difficult to do using the simple techniques I've been using.
