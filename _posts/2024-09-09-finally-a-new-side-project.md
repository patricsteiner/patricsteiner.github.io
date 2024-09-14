---
layout: post 
title: Finally a new Side Project
#image: 
categories: [angular]
---

A story about a new side project.

### Day one: 08.09.2024

I have been enormously absorbed working on [Jobeagle](https://jobeagle.io) the last 2.5 years. I don't think I have ever had such a long streak NOT having any side-project... But yesterday evening I had the sudden urge to start something new. I didn't even have an idea yet. So I brainstormed for around 2 minutes and thought "why not do some workout app?". Sort of stupid, because there are already thousands out there. But even though I have been working out for 10 years now, I have never, NEVER used any workout app. Never needed one. So exactly zero reasons to build one 😂. But hey, I didn't even care about what to build, I just wanted to make something fresh. 

So the first hour was obviously wasted on finding a name + domain. I wanted something with "wod" in it (workout of the day), and eventually settled on [wodz.me](https://wodz.me). I initially wanted a .club domain, but since they cost more than 5$, I obviously went for the cheaper option of .me for 2$, which is also cool.

Ok great, so I now have a domain, but still zero idea what my product should even do. 

Whatever, I thought, let's just set up a new Angular project and go with the flow.

I did exactly that, and 2 hours later I had this beauty online: A server side rendered Angular app hosted on the new experimental firebase app hosting (which sadly only seems to be only available in the US datacenter for now) using tailwind, which has been on my list for a while now.

**What I achieved:**

- A fresh Angular app is up and running, server-side rendered with prettier and tailwind set up
- A cool bear logo (obviously AI generated)
- A Button that generates a random (tough) workout using gemini 1.5 flash (it is so good!)

**Time spent: 3 hours**

<img src="/images/wodz/wodz-day1.png" width="400"/>

### Day two: 09.09.2024

Decided to hold myself accountable and write a blog post about my new side project, reviving my blog in the process. While doing this, I decided to take it up even further and set the goal to earn at least 1$ with this project. I have no idea how, but I will figure it out.

**What I achieved:**

- Revived my blog
- Set a goal to earn at least 1$ with this project
- (Didn't have time to actually code on the project 😅)

**Time spent: 1 hour**

### Day three: 10.09.2024

**What I achieved:**
- Added a new feature that let's you configure the WOD before generating.
- Read an official VertexAI Prompt Engineering Guide and improved the wod generation
- Thought about SEO friendlier domain names, since the goal has now changed to not just be a side-project, but to actually make money. wodgenerator.ai maybe?
- Spent around 1.5h for a new fancy retro style logo (begone bear :()
- Spent another 2h to make buttons look and behave fancy 😎

**Time spent: 4 hours**

<img src="/images/wodz/wodz-day3.gif" width="400"/>

### Day four: 13.09.2024
Did a lot of research on component frameworks (part of the reason I do this whole project here is to find the "perfect" stack for a jobeagle rewrite). I love Ionic, but it's all web-component based, which sadly is not a good fit for SSR apps (which is almost non-negotiable for our jobeagle frontend).
Shadcn is the new cool kid on the block, but it's react based - spartanUI looks like the angular equivalent, but to me, it doesn't really hold the premise of "you own the code", because there is so much additional stuff to include for their components to work... Sure, I could own and adjust it, but I don't want to learn yet another framework. Another option would be Angular Material, obviously, but it is too opinionated and too hard to adjust (I want to build some fresh retro-style UI).
I guess sticking with plain tailwind won't even be that bad. Yes, I will have to write all components myself, but honestly, I think it will be less hassle than trying to adjust a component framework to my needs.

**What I achieved:**
- Decided to stick with plain tailwind instead of any component lib (for now)
- Built basic firestore persistence for the WODs and results (although in a very primitive string-only way --> will be adjusted to actual data model).
- Figured it might be easier and better to just implement my own ("type-safe") WOD generator instead of using string-only AI results.

**Time spent: 2 hours**

### Day five: 14.09.2024
Spent around 3 hours debugging an SSR build issue. Takeaway: We need to assure the app becomes stable (in terms of "not having any pending async operations") when rendered on the client AND server. And the latter can be tricky if using firestore realtime subscriptions... Need to be careful with `filter(Boolean)`s etc, because they might block the app.

**What I achieved:**
- Implemented a JSON schema for WODs instead of just using strings
- Some cosmetics
- Thought about making this an app for coaches to generate and re-sell WODs, so I don't have to sell it. On the other hand, maybe that's exactly the value I could generate: Be the AI coach for end-users, and have them pay less than they would for a coach selling them a random WOD.

**Time spent: 6 hours** (way too much,  haven't really achieved much because I don't have a clear goal)
