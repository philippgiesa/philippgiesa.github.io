---
title: "If the environment agency won't do it, I'll do it myself - Part 1: Air-Quality-Sensor" 
subtitle: Setting up the Raspberry Pi with the Enviro+ and the PMS5003 particulate sensor
date: 2024-05-29
tags: ["raspberry", "pi", "enviro", "PMS5003", "monitoring"]
---
## How this project came about
First of all, I like nerdy stuff and I always wanted to have a side-project. But I never had an idea good enough, where I wanted to invest the time. And the time invest is real. But it's fun. So much fun.

So, I live in a residential complex and the first floor is commercially used. The usage of the space below my apartement was intended for offices or retail. But the newest tenant is a small restaurant-chain serving mainly burgers and fries. Yay! What's more, the ventilation is on ground-level, directly below the bedroom windows and balconies. Yay!

{{< figure src="/img/meme_zero_days_without.png" caption="Figure 1: I can't enjoy burger anymore. Thanks. [[Imgflip](https://www.imgflip.com/)]" width="300px" >}}

Long story short, the smell is unbearable. I asked the environmental agency to monitor the air-quality and they only said something about "community of ownership ... yada yada yada ... not responsible ... yada yada yada ...".
Long story even shorter, I'm not giving up on that front, but I said to myself. If they won't monitor the emissions and air-quality, then I will.

And so, the idea was born, to build an outdoor air-quality-monitoring station.

This blog post is heavily inspired by [Ganesh Shankar](https://curiositysavestheplanet.com/air-quality-monitor-raspberry-pi-enviro-maker-project/) and [Sandy Macdonald](https://learn.pimoroni.com/article/enviro-plus-and-luftdaten-air-quality-station) when it comes to the outdoor sensor and again [Sandy Macdonald](https://sandyjmacdonald.github.io/2021/12/29/setting-up-influxdb-and-grafana-on-the-raspberry-pi-4/) in terms of saving and analyzing the data.

## Necessary (and optional) hardware
There are some things you absolutely need, and some things are optional.

The necessary bits and pieces are:
- **Raspberry Pi Zero W(H)**: If you don't know how to (or don't want to) solder, get the WH with the GPIO pins. It's like 1€ more.
- **Enviro+ Air Quality pHAT**: This will be the central air-quality sensor for your kit. However, it cannot measure particulates. For that you need in addition ...
- **PMS5003 particulate sensor**: This sensor measures PM1, PM2.5 and PM10 particulates in the air. You can probably connect it directly to the raspberry, with the Enviro+ it's more or less plug&play and I like to have the option to measure gas, temperature, humidity and so on.
- **Micro SD card**: Get a micro SD card (maybe already a pre-installed card with Raspberry Pi OS, but installing the OS on the Micro SD is a piece of cake and will only take a few minutes)
- **Raspberry Pi charger** *or* **Old Phone Charger (with enough ampere)**: At least while setting everything up, you devices need power. Any old phone charger with enough ampere is fine. I like to use one with 5V 2A. If I remember correctly the power draw of the Pi Zero is something around 1A, plus the HAT plus the sensor. With 2A, I never had any problems.
- **Weatherproof casing**: If you want to build an outdoor air-quality sensor, some form of weatherproof casing is absolutely necessary. Note that it can't be airtight, the sensors need fresh (or not so fresh) air.
    - If you want it cheap, get some angled drain or aircon pipes (preferably with a rectangular shape), so that air can get to the sensors from underneath, but the electronics stay dry when it rains.
    - If you want if fancier, get a small distribution board like the Spelsberg AKi05 (that's what I'm planning to get, more on that in a feature blog post). It's IP65, has knockouts for cable management and also for your sensors. And it has a door for easy accessability.
- **PiBow case**: Well, not really necessary, but I'd recommend some casing for the Pi Zero.

That should be everything you need.

But maybe not everything you want.

This set-up relies on a cabled power source (your charger). So, you need a wall socket or a way to lay a cable to your preferred spot for your air-quality sensor. Mine is on the windowsill with no wall socket in reach and no reasonable way to lay a cable.

{{< figure src="/img/meme_solar_power_exit.png" caption="Figure 2: It's still a budget project (nobody said how large the budget has to be...) [[Imgflip](https://www.imgflip.com/)]" width="400px">}}

Let's get solar power into the game. If you want to go crazy - like me - then you'll need:

- **Solar panel**: Okay, but how much Watt does it need? I haven't tested it yet (I'll update this post, once I did), but I did some research and calculations (see below). For now, let me recommend something between 6-10 Watts with 6V. For example from Voltaic Energy. They are pricy, but build with Outdoor IoT in mind and also compatible and tested with the next piece you'll need.
- **Battery pack aka Powerbank**: The sun does not shine during the night (no shit, Sherlock). So you need a power backup. Okay, just get a cheap powerbank, but here comes the tricky part. They shut off, when there is no or very low power consumption and that's the problem. The power draw of the Raspberry Pi will likely be to low, the powerbank will shut off and the raspberry will perform a hard shutdown (which it does not like, btw. they are very sensitive in that regard and react with data corruption). So you'll need a battery pack with an always-on function (e.g. it always stays on or it stays on, once something is plugged in). Plus, you'll need pass-through charging. You'll want to charge the battery pack with your solar panel and simultaneously power your Raspberry. My recommendation is again a battery pack from Voltaic Systems (a V50 should be enough).
- **USB fan**: Since it can get hot inside the case, I got a USB fan for the hot days in summer to provide cooler air for all devices.

At this point, note that you're not protected from a power loss event. The battery pack might run low, the solar panels do not provide enough energy and a hard shutdown might be the consequence. You can - and I might - build another safety net, namely a UPS (uninterruptible power supply). I won't go into details at this point, but basically you'll have another small board and another (small) battery pack, which kicks in when the primary power source breaks down. Once that happens, it'll tell the Raspberry Pi to shut down safely. You can buy these online (some have mixed or bad reviews and/or are expensive) or find tutorials to make one yourself.

For now, that's all you need. :)

## A word on the Solar panel
Okay, I could do the hard math, like calculating average power consumption, looking up average sun hours and intensity and then calculating how large my solar panel has to be. I didn't. I did measure the power consumption of my Raspberry Pi with all sensors though and it drew between 1.2 and 1.7 Watt (it has to be said that the inaccuracy of my power meter at around 1W is quite high). Factoring in that the solar panel has to charge the drained battery pack after each night, i.e. deliver more power during the day than is needed, and that it won't reach its peak power on most days, I'd recommend at least 6W, probably 8-10W to be on the somewhat safe side.
My plan is to get the 10W solar panel and also have the charge-indicator of the powerbank visible, so that I can check the battery level and top up with a cable, if needed.

## Hardware assembly
Now let's prepare everything, assemble the hardware and get going.

First you need to install Raspberry Pi OS on the Micro SD card.

The final thing:
{{< figure src="/img/pi_enviro_sensor_assembled.jpg" caption="Figure 3: Pi Zero with Enviro+ HAT and attached PMS5003" width="600px" >}} 
