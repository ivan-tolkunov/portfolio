---
title: "Smart Bottle"
---

Video: [YouTube](https://www.youtube.com/watch?v=Yq9PTi10ka4)

---

## Concept

Every day I drink water. During coding sessions or when doing other stuff on PC, I consume a very small amount of water per day. This is not good for my health. If I don't drink 2-3 liters of water, I will really feel bad. There is also another part of me. This part drinks really big amounts. In this case, I will feel bad during the rest of the day.

To avoid this scenario I decided to make a device that will inform me about water consumption per day. I started thinking about what it could be. So, it should be a device that will be a part of the bottle and show information to a user.

A user interface should be accessible everywhere. It can be a screen on a device, mobile app, or website. After brainstorming, I decided to create a _React Native app_. So, in this way, I will have an app on all my devices.

The next step is to understand how this device will communicate with a React Native app. A small investigation gave me the idea to make a _server_, that can receive data from a development device, and send it to a React Native app. Also, this server will store all data in the _database_ (all devices with access to a React Native app must show valid data).

How the device should look like? I browsed sensors that can measure water level. There are two types: float switches, and capacitance sensors. These sensors can measure water level but it's not my case. I wanted to create a small device that would not change form and interact with water by itself. After research, I found that it's possible to use distance sensors to measure water levels.

This sensor will be in a lid, so the sensor must be waterproof and small. I found _VL53L0X_ Time-of-Flight ranging sensor. This sensor has a start measure point of 2 mm, small size, and glass which protects it from water.

So, what do we have? The device's form will be lit, a VL53L0X sensor for measuring the amount of water in a bottle, a server on Java/Spring Boot, and a React Native app.

---

## Implementation
