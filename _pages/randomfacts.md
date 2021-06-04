---
layout: page
title: Random stuff I learned
permalink: /randomfacts/
---

### Random fact #10: Spontaneous hay combustion - 04.06.2021
Wet hay can ignite by itself. This first sounded very counter-intuitive to me - imagine trying to make a campfire with wet hay, it sounds almost impossible. _Dry_ hay sounds plausible to me, but _wet_ hay? How can hay ignite itself?

It turns out that with a hay moisture of more than 25%, microbiological activity will start to happen (i.e. mold and bacteria). This produces heat. With enough water and air, metabolism rate further increases and temperature can raise up to 70°C. The decomposition of pectins (some kind of sugar) and proteins can further increase the temperature and produce pyrophoric gases and vapors which self-ignite with temperatures below 100°C --> 🔥🤯.

Source: [German article about Heuselbstentzündung](https://de.wikipedia.org/wiki/Heuselbstentz%C3%BCndung)


### Random fact #9: Doping in elite level sports - 03.04.2021
Elite level sport competitions are very corrupt, especially the olympics. Almost every top athlete uses [PEDs](https://en.wikipedia.org/wiki/Performance-enhancing_substance).
It's an illusion to think you can win with talent and hard work only. Because someone else with talent and hard work _and_ PEDs will beat you. So if you want to win, you need PEDs (and thus harm your body).
Anti-doping, which is said to bring safety and fairness to sport competitions, causes quite the opposite of what it is supposed to be doing:
- Instead of using safe and well researched compounds, athletes use dangerous and untested substances.
- Different countries have vastly different (or no) drug testing policies which makes international competitions even more unfair.
- Testing itself is very difficult and unfair, even if people don't cheat. Genetics and enzymes (or absence thereof) have a very high impact on results of testosterone tests for example.
- Anti-doping demonizes PEDs and their research (some of which have clinical uses).
- It brainwashes the public (esp. children) to believe that sport is fair and no top athlete uses drugs.

Unfortunately there is no easy solution to this problem, but it surely seems that the prohibition of drugs causes more harm than good.

Source: [This very well put together and backed up video by Clarence Kennedy, a popular Irish weightlifter](https://www.youtube.com/watch?v=HQLweuRSD9M)

### Random fact #8: Slow down aging - 16.01.2021
There are different theories of what exactly aging is and how it is caused, but fundamentally, it comes down to loss of information in our cells.
One __hypothesis__ is, that aging is caused not by loss of information in the DNA, but in the [epigenome](https://en.wikipedia.org/wiki/Epigenome). 
The epigenome basically tells the cell, what kind of cell it is by "turning on or off" some parts of the DNA in this cell.
If this information is lost, a cell is not reading the correct genes anymore and basically starts doing things it is not supposed to do.
What's interesting is that there are longevity genes that can prevent this effect. They are "activated" when conditions get tougher and then 
instead of growing and reproducing, cells go in some kind of protection mode.
Simply put, we can make conditions tougher and activate the longevity genes by caloric restriction (fasting), hot/cold exposure and doing [HIIT](https://en.wikipedia.org/wiki/High-intensity_interval_training).

Source: [Veritasium in collaboration with professor David Sinclair](https://www.youtube.com/watch?v=QRt7LjqJ45k)


### Random fact #7: Turn weaknesses into strengths - 19.11.2020
I actually learned this one around 10 years ago from my parkour trainer. Just because I have a certain weakness, does not mean this cannot be changed. I just need to make a conscious decision and with enough determination, it can be turned into a strength. I already proved this to myself many times over the last years. Today, two days after injuring my already-weak knee, I once again decide to turn a weakness into a strength. It will take time, it will take a lot of effort, but I will not stop working on the injury until my injured knee is at least as strong as the other one, and I can jump higher than ever 😁


### Random fact #6: Common keyboard layouts are terrible for desktop computers - 10.10.2020
The QWERTY/QWERTZ keyboard layout was designed for typewriters. The keys are arranged in such a way that letters which are often used together, are far away from each other, so they cannot be pressed quickly enough and cause jamming of keys. But modern keyboards don't jam! Other layouts, such as DVORAK, Colemak and Workman, address this issue and _drastically_ reduce the movement of fingers and thus increase typing speed and decrease chance of injury. Might be a good idea to switch at some point... 

![QWERTY vs. DVORAK heatmap](https://i.imgur.com/iVJx4OQ.png)


### Random fact #5: Binary search for git commits - 03.09.2020
When trying to find a commit that broke a piece of code, `git bisect start` can be used to initiate a binary search for the commit. Type `git bisect bad`, meaning current revision is broken, and then `git bisect good HASH_OF_A_WORKING_REVISION`. A revision in the middle of good and bad will then be checked out. Test if it works and type either `git bisect good` or `git bisect bad`. Repeat the process until you find the commit that broke the code. Then end with `git bisect reset` to return to the HEAD 🙌.


### Random fact #4: "En passant" chess rule - 25.05.2020
When moving a pawn two squares, in the very next turn (and only in this turn) it is allowed for an opposing pawn to capture the moved pawn as if it only moved one square. This only applies to pawns, no other unit can capture a pawn "en passant".

![En passant move in chess](https://i.imgur.com/YJskQYY.gif)


### Random fact #3: How to read piano notes - 29.04.2020
There are two clefs for piano sheet music: The treble clef for the higher notes (left hand) and the bass clef for the lower notes (right hand).
The middle C is on the line between the two clefs, as can be seen in the image below. There is a pattern that is easy to remember, from which all other notes in the scale (C, D, E, F, G, A, H) can be derived.

![Treble and bass clef notes](https://i.imgur.com/3UBiwau.png "Treble and bass clef notes")


### Random fact #2: How a Chrome extension works - 21.04.2020
Chrome extensions allow us to extend the functionality of the browser or specific websites. We can for example block or redirect requests, preload content in new tabs or implement our own dark mode for certain pages. A Chrome extension is written in JavaScript/TypeScript and made out of the following parts:

- Manifest: Mandatory file that contains information and capabilities of the extension.
- Background script: basically a listener and handler for browser events (e.g. tab opened, url requested, ...). Executed in the background, cannot access pages/DOM.
- Content Script: JS that executes in the context of a page i.e. it can read and modify the DOM.
- Options Page: Enables customization of the extension.
- Optional UI: Simple HTML & CSS, e.g. a popup to ineract with the extension.

Communication between content and background scripts and even betweeen extensions is done using the storage API (Local Storage) or messaging (e.g. `chrome.tabs.sendMessage(tabId, message)`)

Resources: [Chrome extension developer documentation](https://developer.chrome.com/extensions/overview)


### Random fact #1: How our lungs work - 19.04.2020
Humans have two lungs which are directly in front of the heart. The lungs are comparable to a balloon filled with a sponge. When we breathe in, the air goes through the trachea (Luftröhre in german). Mucus in the trachea prevents dust etc. to get into the lungs. From the trachea the air goes into the lungs, which are structured like a tree that branches into smaller and smaller pipes (called bronchioles), until eventually the air gets into little air sacks called alveoli. These air sacks are surrounded by capillares filled with red blood cells, containing a protein called hemoglobin. Because of diffusion (molecules in gas move where there is a lower concentration of their kind) the C02 in the blood will be unloaded into the lungs (and later exhaled) and the 02 will go to the hemoglobin and transported through the body via the bloodstream.

Sidenote: For people who have asthma, the cartilage rings in the trachea collapse, which makes it harder for air to go through. Medicine helps to open it back up.

Sources:
- [Some dude dissecting lungs](https://www.youtube.com/watch?v=9xhxALk9gm8)
- [TED-Ed video](https://www.youtube.com/watch?v=8NUxvJS-_0k)
