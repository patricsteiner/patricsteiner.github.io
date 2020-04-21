---
layout: page
title: Random stuff I learned
permalink: /randomfacts/
---

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
