---
layout: post
title:  "How to port SNES games to Nintendo DS with static recompilation"
date:   2016-12-31 12:00 -0400
---
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

![]({{ site.url }}/assets/ff4.png)
{: style="text-align: center"}

Here you can see an excerpt of the disassembly of the port of Final Fantasy IV **for PlayStation**.
In MIPS assembly, the instruction `jal` is roughly equivalent to a function call: it saves the return
address in the `$ra` register and jumps to the target instruction.
For better understanding, I've assigned names to subroutines so that it's easier to see what's going on.

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
parts by hands, it looks like it's possible to achieve a fully functional port.
So the next question I asked myself was: **can I do this myself**? Can I port some of this old games
on a recent platform, and maybe enhance them in the process?

Compared to TOSE, I start from a much more difficult position. Realistically, their team had access
to the original, commented source code and graphics resources. In comparison, I only have a flat ROM
with no symbol informations. I'm basically **clueless about the meaning of any byte** in the cartridge.
Some of them may be code, others graphics, music, event scripting, etc.

In general, accurate disassembly is not a solved problem. In particular, 65816 assembly supports
both 8 and 16 bits mode for accumulator and index registers, so the **size of the instructions** can
vary depending on the mode the processor is in. Disassembling the same portion of code can yield different
results and only one of them is usually correct. Furthermore, indirect **jumps and jump tables** are
not completely reversable at static time. Heuristics can yield both false positives and false negatives.
The whole process is very error prone. But can we get close enough? **If we can get a disassembler that
is 90-95% accurate** and provides enough auxiliary tools to reverse the uncertain parts, it would
still be extremely valuable for the task.

# Assembly and C code generation

# Fixes to actually make it work

