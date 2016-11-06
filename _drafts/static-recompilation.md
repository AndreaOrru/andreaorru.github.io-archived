---
layout: post
title:  "How to port SNES games to Nintendo DS with static recompilation"
date:   2016-12-31 12:00 -0400
---

# Introduction

If you're a ~~nerd~~ passionate retrogamer, you have probably played some good old Super Nintendo games.
If you're anywhere near my level of ~~nerdiness~~ passion, you may have even played some
of the old-school Squaresoft titles like *Final Fantasy* or *Chrono Trigger*.

Later on, some these games have been **ported to more recent platforms** to give today's kids
a chance to play these masterpieces (and to compensate SquareEnix's lack of ideas for new games).
Chrono Trigger received a (slightly) enhanced remake that runs on the Nintendo DS:

![]({{ site.url }}/assets/ctds.jpg)
{: style="text-align: center"}

So how did Squaresoft do this? They obviously didn't pay a full team of programmers to rewrite it from scratch.
And they couldn't just recompile the original code, as those games were largely **written in 65816 assembly**.
So what's going on here?

# What's going on here?

Here you can see an excerpt of the disassembly of the port of Final Fantasy IV **for PlayStation**.
In MIPS assembly, the instruction `jal` is roughly equivalent to a function call: it saves the return
address in the `$ra` register and jumps to the target instruction.
For better understanding, I've assigned names to subroutines so that it's easier to see what's going on.

![]({{ site.url }}/assets/ff4.png)
{: style="text-align: center"}

Notice anything odd? If you are familiar with the [6502/65816 instruction set](http://www.defence-force.org/computing/oric/coding/annexe_2/)
you will recognize the names of some of the opcodes. `PHX` for example, saves the X register on the stack;
`ROL` reperesents the bitwise rotation to the left. As further proof, `_read_sfc_memory` sounds a lot like
it's **loading something from SNES memory**.

You can see where this is going: TOSE, the company in charge of the port, most likely run the
original code through some kind of **automatic conversion process**.
This is super cool - but also highly unefficient. The emulation functions are not even optimized
inline. No wonders the PlayStation ports exhibited very poor performances.

When I discovered this, I was blown away. The PlayStation **doesn't have enough computational power
to simply emulate the SNES**. But through static recompilation and maybe rewriting some critical
parts by hand, it looks like it's possible to achieve a fully functional port.
So the next question I asked myself was: can I do this myself? **Can I port some of this old games
on a recent platform like the Nintendo DS**, and potentially enhance them in the process?

# Why is this a tough problem?

Compared to TOSE, I start from a much more difficult position. Realistically, their team had access
to the original, commented source code and data resources. In comparison, I only have a flat ROM
with no segments and no symbol informations whatsoever.
I'm basically **clueless about the meaning of all the bytes** in the cartridge.
Some of them may be code, others graphics, music, event script, etc.

In general, accurate disassembly is not a solved problem. In particular, 65816 assembly supports
both 8 and 16 bits mode for accumulator and index registers, so the **size of the instructions** can
vary depending on the mode the processor is in. Disassembling the same portion of code can yield different
results and only one of them is usually correct. Furthermore, indirect **jumps and jump tables** are
not completely reversable at static time. Heuristics can yield both false positives and false negatives.
The whole process is thus very error prone. But can we get close enough? **If we can get a disassembler that
is 90-95% accurate** and provides enough auxiliary tools to reverse the uncertain parts, it would
still be extremely valuable for the task.

The current state of the art for disassemblers is [IDA Pro](https://www.hex-rays.com).
Unfortunately, its support for 65816 and SNES ROMs is quite limited and inadequate for
our purposes. Furthermore, it's a *very* expensive piece of software.
Looks like we are on our own here.

And of course, disassembling is just the beginning. We have to discover where the various pieces
of graphics are, when and how they are sent to the screen, what's their format and how to convert
it to so it's compatible with the Nintendo DS. There are countless of other small details:
hardware registers on the SNES (i.e. for joysticks) have to be **mapped to equivalent registers
on the DS**... You get the idea.

The result of this process still has to be hackable in order to be able to fix bugs and
optimize performance critical spots. And after all of this is done, you still have to
**generate decently optimized ARM code** for the DS.
The programming equivalent of a walk in the park, don't you agree?

# Devising a strategy

- Start from the log of an emulator to get accurate disassembly and runtime information
about data and code references
- Use "static execution" and heuristics to discover potential new pieces of code
- Once it's done, generate the disassembly, test that the games works/has the same hash
- Decompile to C and dump comments with information
- Patch raw spots
- PROFIT
