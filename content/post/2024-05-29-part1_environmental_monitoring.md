---
title: "If the environment agency won't do it, I'll do it myself - Part 1: Air-Quality-Sensor" 
subtitle: Setting up the Raspberry Pi with the Enviro+ and the PMS5003 particulate sensor
date: 2024-05-29
tags: ["raspberry", "pi", "enviro", "PMS5003", "monitoring"]
---
## How this project came about
First of all, I like nerdy stuff and I always wanted to have a side-project. But I never had an idea good enough, where I wanted to invest the time. And the time invest is real. But it's fun. So much fun.

So, I live in a residential complex and the first floor is commercially used. The usage of the space below my appartement was intended for offices or retail. But the newest tenant is a small restraurant-chain serving mainly burgers and fries. Yay! What's more, the ventilation is on groundlevel, directly below the bedroom windows and balconies. Yay!

{{< figure src="/static/images/meme_zero_days.png" title="Zero Days Without" width="300px">}}

Long story short, the smell is unbearable. I asked the environmental agency to monitor the air-quality and they only said something about "community of ownership ... yada yada yada ... not responsible ... yada yada yada ...".
Long story even shorter, I'm not giving up on that front, but I said to myself. If they won't monitor the emissions and air-quailty, then I will.

And so, the idea was born, to build an outdoor air-quality-monitoring station. 

## Hardware assembly
Connect Pi to Enviro.