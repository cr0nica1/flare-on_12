---
layout: post
title: The 12th annual Flare-On challenge Solution
gh-repo: cr0nica1/infosec
tags: [flareon12]
comments: true
mathjax: true
author: Hoàng Tiến Minh
---

The Flare-On Challenge is a reverse engineering contest held every year by the FLARE team (Mandiant, Google Cloud Security) for all reverse engineers and malware analysts. This year, it had only 9 challenges instead of 10, spread over 4 weeks, and some of them were quite strange and difficult. Luckily, I finished the contest on the 27th day (it lasted for 28 days, by the way).

![Flareon profile]({{ '/assets/img/flareon12/finisher_picture.png' | relative_url }})

This year, Vietnam had 27 finishers, taking the top spot in the winners’ circle. I’m very proud that we achieved the highest number of finishers by country for the second year in a row, proving how excellent Vietnamese reverse engineers and malware analysts are.

![Flareon profile]({{ '/assets/img/flareon12/finisher-by-country.png' | relative_url }})

Here is my solution for this year’s Flare-On Challenge. Please feel free to share your feedback if you have a better solution!


## Challenge 1: Drill Baby Drill!
![Chall1-description]({{ '/assets/img/flareon12/chall1.png' | relative_url }})

The challenge provides a Python script implementing a 'find the bear' game. The objective is to retrieve the flag, which is revealed upon successful completion. To win, the player must correctly 'drill' the 'bear' location on each level without hitting any 'rocks'. The game consists of multiple levels, each containing a single 'bear', and the level sequence is randomized.
![Game-UI]({{ '/assets/img/flareon12/chall1-pic1.png' | relative_url }})

**Analyzing the winning mechanism**

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




