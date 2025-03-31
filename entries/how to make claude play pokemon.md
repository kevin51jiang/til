---
title: how to make claude play pokemon
permalink: how-to-make-claude-play-pokemon
date: 2025-03-31T00:24:49-07:00
tags:
---

Today I went to the [Claude Plays Pokemon](http://lu.ma/poke) mini hackathon.
The goal was to get through the Mt Moon labyrinth, and nobody finished.

Not much was done, but here's some takeaways:

- Sonnet 3.7 is very bad at vision for gameboy. Some people experimented with
  upscaling/coloring in the screenshots. Other people just told Sonnet that it
  was visually impaired and not to trust what it was seeing.
- The idea of playing with the multiverse (trying multiple paths and then only
  contiuing with the "best") is pretty interesting and could be effective.
- The emulator/memory has it so the border of the map (lines where x=0, y=0) is
  walkable tiles, but you actually can't walk there. This is what led to Claude
  getting stuck for one team.
