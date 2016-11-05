---
layout: post
title:  "Porting Super Nintendo games to Nintendo DS through static recompilation"
date:   2016-12-31 12:00 -0500
---
If you're a ~~nerd~~ passionate retrogamer, you have probably played some good old SNES games.
If you're anywhere near my level of ~~nerdiness~~ passion, you may have even played some
of the old-school Squaresoft titles like *Final Fantasy* or *Chrono Trigger*.

Later on, some these games have been ported to more recent platforms to give today's kids
a chance to play these masterpieces (and to compensate SquareEnix's lack of ideas for new games).
As an example, Final Fantasy IV, V and VI were ported to PlayStation with the addition of some
cool pre-rendered cutscenes. Chrono Trigger even received a (slightly) enhanced remake that
runs on the Nintendo DS:

![]({{ site.url }}/assets/ctds.jpg)
{: style="text-align: center"}

Cool, isn't it? Now, if you are into tech stuff (and if you're reading this blogpost I assume you are),
you're probably wondering: how did SquareSoft do this? Did they pay a full team of programmers to rewrite
it from scratch? No way, not worth all those resources. Did they just recompile the original code? Impossible,
as those games were largely written in 65816 assembly. So what's going on here?
