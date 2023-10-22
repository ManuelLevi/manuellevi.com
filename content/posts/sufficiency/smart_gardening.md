---
title: "Smart Gardening with ESP8266"
date: 2023-09-25T16:18:12+01:00
draft: false
---

This is still a draft and I will keep updating this as it develops.

## Current situation 

I have spinach, tomatos and some herbs like basil growing in my balcony.

## Why grow my own food?

* Food Quality and Control:
    * Avoid harmful chemicals, such as pesticides and herbicides.
    * Enjoy all-natural, healthier food.
* Increased choices: you can grow species that aren't commonly found for sale;
* Financial Benefits: Save money while having high quality vegetables (this adds up!);
* Stop large, corrupt and inneficient governments: Because they are no financial transactions involved, there isn't anything for the government can involuntarily take from you (taxes and other fees);
* Feels good: when you eat your own food, there's a sense of 
self-sufficiency and empowerment.

## Why automate it?

I want to automate this for various reasons:
* I sometimes forget to water. It's hard to build this habit as it is dependent on weather conditions and planted vegetables.
* I want to be able to go on vacation without worries my plants will dry up.
* I want to maximize yield for my small space.

## Water source

The system will consume water from a tank.

This tank should collect water from rain on rainy days, but can also be filled with tap water if necessary.

While I want some water savings, using rain water, I rather have a lighter water tank than a heavier one. 

## Water collection

The system will be on a residencial balcony, and I need to develop a way to collect water from the rain.

I do not want the water to be collected from the rain spouts as I want to keep this as permissionless as possible.

Depending on the amount of time and materials necessary, I might to 3D model and print custom water collection parts for the balcony. If not, I'll adapt.

## Sensors

The system will have sensors to let us know the soil humidity.

Ideally, the sensors should be weatherproof and based on capacitance rather than resistance.

## Pumps

When the soil moisture drops below a certain threshold for a specified period of time, the water pump will turn on for a set duration. Then it will pause, allowing the water to permiate the soil. After this pause, humidity level will be read again, and if it's still not at a defined level, the pump will turn again for that same period of time.

## Distributed but indepedent

The system will be IoT enabled, but it will function even without WiFi access.

## Why build another smart gardening system?

This has been done thousands of times, however, I have particular requirements:
* Offline operation: The system should work without WiFi
* Battery backup: If mains fail, the system should still be able to keep running for a day or two.
* It should be based on affordable components like the ESP8266;

# System components:

* Water tank;
* Water level sensor (to notify when water level is low);
* Moisture sensor (preferably capacitive for longevity);
* Microprocessor with WiFi access (ESP8266);
* Relay to turn the pump on and off;
* Water pump;

# Pseudocode

```
while True:
    if waterlevel < water_level_threshold:
        send_notification()
    else:
        soil_moisture = read_moisture()

        while (soil_moisture < watering_threshold):
            turn_pump_on()
            wait(pump_on_time)
            turn_pump_off()
            wait(pump_off_time)
    deep_sleep(30 minutes)
```
That's it!

# More Features

This is when my engineering nature betrays me.

Immediately, I start thinking about additional features I can add to the system:

1. **Easy WiFi configuration:** This feature would allow users to easily configure the WiFi settings after the system has been deployed. It would provide convenience and flexibility.

2. **Admin dashboard:** An admin dashboard would enable users to change threshold and sleep variables, among other settings. This would give users more control over the system and allow for customization.

3. **MQTT integration:** Integrating MQTT (Message Queuing Telemetry Transport) would enhance the system's communication capabilities, enabling it to interact with other devices and platforms.

However, it's at these times where I need to remind myself of the objective: **water my plants when needed.** I need to stay focused on the core functionality of the system and avoid unnecessary distractions.

I don't need more distractions. 

* I don't need to know how fast the water in my soil evaporates. This level of detail is not essential for the system's primary purpose. 

* I don't need to super optimize the threshold levels, the time the water pump stays on and off, etc.

* I don't need an extra maintenance and risk of having another device connected to the internet all the time. Adding more devices connected to the internet increases the potential vulnerabilities and risks associated with the system, adds more radio noise, uses up more energy unecessarily and so on.

By keeping the system simple and focused on its main objective, I can ensure its reliability and effectiveness.