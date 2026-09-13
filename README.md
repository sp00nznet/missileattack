# Missile Attack! — Static Recompilation

Static recompilation of **Missile Attack! v1.1** (Peter Siamidis, 1992) from
its shipping Win16 binary to native C.

Built on the [pcrecomp](https://github.com/sp00nznet/pcrecomp) toolchain.

## Project Status: **P0 complete, P1 not started**

---

## What this is for

This is the smallest complete Win16 game in the collection, and that is the
whole point of it.

```
MISSILE.EXE   87,360 bytes   NE, Microsoft linker 5.14, Windows, PROTMODE
              2 segments (1 CODE, 1 DATA), 20,975 bytes of code
              165 relocations -- 152 of them imports, 13 internal
              imports: MMSYSTEM, win87em, KERNEL, GDI, USER
```

**One code segment. Twenty-one kilobytes.** Every other NE project here —
El-Fish (121 segments), Bang! Bang!, Microsoft Bob, Catz, The Even More
Incredible Machine (34 segments) — has segmentation as a first-class problem.
This one does not have segmentation at all.

That makes it the right fixture for the 16-bit pipeline itself. A change to
`ne_parse.py`, `ne_decode.py`, `ne_xref.py` or `lift16.py` can be checked
end-to-end here in the time it takes to read the diff, and if it breaks, the
failure is in the change and not in some segment-boundary edge case. The
toolkit's own history says why that matters: three of the five NE bugs found
during Bang! Bang! were off-by-one errors in table parsing that only showed up
as *missing functions*, never as an error message.

It is also a real game with a real window procedure, not a synthetic test:

```
WNDPROC        seg 1:0x01AC
ABOUTMSGPROC   seg 1:0x22A8
HOWTOMSGPROC   seg 1:0x233C
HIGHMSGPROC    seg 1:0x248A     high scores
ENTRYMSGPROC   seg 1:0x23D0
```

Five named entry points across 21 KB means nearly every top-level control path
is identified before disassembly.

## The one interesting bit

It imports **`win87em`** — Borland's 8087 floating-point emulator — alongside
MMSYSTEM. That is an x87 surface in a 16-bit NE image, which is exactly what
forced `disasm/fpu_decode.py` to exist during El-Fish. This is a 21 KB version
of the same problem and a much faster place to test changes to it.

`MISSILE.WAV` and friends go out through MMSYSTEM; four WAVs, no MIDI.

## Where it goes next (P1)

Straight through the standard 16-bit path. There is nothing unusual to plan
around, which is the point.

## Provenance

Shareware, distributed with a $5 registration request:

> If you enjoyed Missile Attack! and want to encourage me to create more games,
> please register your version by sending $5 to: ...

Shareware is not public domain. The binary stays local; `original/` is
gitignored.

## Layout

```
missileattack/
  original/       win3_MissileA.zip
  original/ex/    extracted
  analysis/
  docs/
```

## Credits

Missile Attack! © 1992 Peter Siamidis. This project neither contains nor
distributes any part of it.
