---
layout: post
title: The 12th annual Flare-On challenge Solution
gh-repo: cr0nica1/infosec
tags: [flareon12]
comments: true
mathjax: true
author: Hoàng Tiến Minh
---

The Flare-On Challenge is a reverse engineering contest held every year by the FLARE team (Maniant, Google Cloud Security) for all reverse engineers and malware analysts. This year it has only 9 challenges instead of 10 in 4 week, and some challenges is very strange and difficult. Luckily, i finish this contest at the 27th day (the contest held in 28 days btw). 

![Flareon profile]({{ '/assets/img/flareon12/finisher_picture.png' | relative_url }})

This year, Viet Nam has 27 finishers, takes the stacks the winners circle. I am very proud that we take the most finisher by country back-to-back, prove that how excellent of Vietnamese reverse engineers and malware analysts.

![Flareon profile]({{ '/assets/img/flareon12/finisher-by-country.png' | relative_url }})

Here is my solution of this year flare on challenge. Please give me the feedback if you have great solution.


## Challenge 1: Drill Baby Drill!.

![Chall1-description]({{ '/assets/img/flareon12/chall1.png' | relative_url }})

The challenge provides a Python script implementing a 'find the bear' game. The objective is to retrieve the flag, which is revealed upon successful completion. To win, the player must correctly 'drill' the 'bear' location on each level without hitting any 'rocks'. The game consists of multiple levels, each containing a single 'bear', and the level sequence is randomized.
![Game-UI]({{ '/assets/img/flareon12/chall1-pic1.png' | relative_url }})

### The winning mechanism

A logic flaw was identified in the game's obstacle generation mechanism. The game utilizes a `boulder_layout` array, indexed by the column `x`, to determine obstacle locations. Upon inspection, it was found that for any given `current_level`, the column index corresponding to `len(LevelNames[current_level])` (i.e., the column where `x == len(LevelNames[current_level])`) is hardcoded with the value `-1`.
The game's fail-state (hitting a rock) is triggered by the condition `boulder_level == drill_level`. Since the `boulder_level` at this specific column is set to `-1`, this equality check will never evaluate to true, rendering this column 'safe' (rock-free) by default. This behavior strongly implies that the value `len(LevelNames[current_level])` is the deterministic solution (the 'bear's' column index) for each respective level.

```python
LevelNames = [
    'California',
    'Ohio',
    'Death Valley',
    'Mexico',
    'The Grand Canyon'
]
```


