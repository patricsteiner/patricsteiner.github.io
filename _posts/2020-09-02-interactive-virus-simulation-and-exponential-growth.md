---
layout: post
title: Interactive Virus Simulation and Exponential Growth
image: 2020-09-03/corona.jpg
categories: [simulation, angular]
---

After analysing early swiss corona data in [one of my previous posts](https://patricsteiner.github.io/schweizer-covid-19-datenanalyse-mit-python/) I figured it was indeed necessary for people to become aware of how important it is to follow the measures to prevent the spread of the virus. We hear it all the time: _Wash your hands_, _stay at home_, _wear a mask_. But we don't really feel or see if this actually helps. Of course there are scientific explanations and studies, but still, it is hard to imagine how exponential growth works and that you can literally save lives just by washing your hands. But you can, and you probably should. To help everyone get a better understanding of the situation I created a little interactive simulation that visualizes the impact of several prevention measures.

DISCLAIMER: This is a highly simplified simulation of the real world which is solely based on my own knowledge and perception of viruses. The properties of the simulated virus (such as the average duration, lethality, possible immunity etc.) do not correspond to the corona virus, and the impact of the measures is just a guess by me.

## ლ(ಠ益ಠლ) Give me the link already!
 つ ◕_◕ ༽つ [Here you go](https://patricsteiner.github.io/corona-simulator/)

## Initial scenario - exponential growth
Let's start by looking at a world with 100 people, 1 of them infected with a virus and no special prevention measures. In this first run, the virus only has 1% lethality and 99% of infected people will become immune after the first infection:

![first run of simulation](/images/2020-09-03/run1.gif)

We see that this one person has infected the whole population in no time.

## Washing hands - decreasing infection
Let's increase the hygiene and physical distance a little (this will both decrease the infection probability in some way):

![second run of simulation](/images/2020-09-03/run2.gif)

The curve is already much flatter! 

## Staying home - flattening the curve

Now let's see what happens if as an additional measure, 80% of the population stays home:

![third run of simulation](/images/2020-09-03/run3.gif)

With all these measures together, we can keep the curve very flat. The virus never reaches a critical mass of infected people and thus we can avoid exponential growth and eradicate the virus quite quickly. There is a lot more you can do in this simulation, give it a try if you'd like to 😊.

## Conclusion
 
Now I am well aware that there is some science missing in this simulation. But the point of this simulation is just to give everyone a good overview of how fast a virus can spread, if it is not taken seriously. I hope you liked it. And if not, that's cool too.

If you are interested in the source code, you can [check it out on github](https://github.com/patricsteiner/corona-simulator). Feel free wo add additional features or improvements, pullrequests are always welcome 😁✌
