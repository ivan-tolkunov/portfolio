---
title: "Smart Bottle"
date: "2023-04-21"
---

Video: [YouTube](https://www.youtube.com/watch?v=Yq9PTi10ka4)

---

## Concept

Every day I drink water. During coding sessions or when doing other stuff on PC, I consume a very small amount of water per day. This is not good for my health. If I don't drink 2-3 liters of water, I will really feel bad. There is also another part of me. This part drinks really big amounts. In this case, I will feel bad during the rest of the day.

To avoid this scenario I decided to make a device that will inform me about water consumption per day. I started thinking about what it could be. So, it should be a device that will be a part of the bottle and show information to a user.

A user interface should be accessible everywhere. It can be a screen on a device, mobile app, or website. After brainstorming, I decided to create a React Native app. So, in this way, I will have an app on all my devices.

The next step is to understand how this device will communicate with a React Native app. A small investigation gave me the idea to make a server, that can receive data from a development device, and send it to a React Native app. Also, this server will store all data in the database (all devices with access to a React Native app must show valid data).

How the device should look like? I browsed sensors that can measure water level. There are two types: float switches, and capacitance sensors. These sensors can measure water level but it's not my case. I wanted to create a small device that would not change form and interact with water by itself. After research, I found that it's possible to use distance sensors to measure water levels.

This sensor will be in a lid, so the sensor must be waterproof and small. I found VL53L0X Time-of-Flight ranging sensor. This sensor has a start measure point of 2 mm, small size, and glass which protects it from water.

So, what do I have? The device's form will be a lid, a VL53L0X sensor for measuring the amount of water in a bottle, a server on Java/Spring Boot, and a React Native app.

---

## Implementation

I chose an ESP32 microcontroller as the brain for the system. It is a web server for configuration, receiving and analyzing information from sensors and sending data to the server.

The user connects to the ESP32 via WI-FI and opens the configuration page to connect the microcontroller to the Internet.

So, how can I calculate the amount of water using the VL53L0X Time-of-Flight range sensor? The distance sensor can measure the distance from the lid to the water. When the laser hits the water, it deflects, and the sensor calculates the time from peak to deflection, which is translated into millimeters. I just subtract the new distance from the previous distance and then send that value to the server.

On the server, I get the data from the ESP32 and transfer it in liters. To do this, I used the formula for the volume of a cylinder V = πr²h. Then I save the new data to the database.

The database has three fields: amount, date, and time. In amount, I store data about water consumption. Date is used to show statistics during the month. Time is for detailed information about water consumption during the day.

A server deployed on Google Cloud, I did so for a stable connection and it's free for non-commercial use.

In Fusion 360 I modeled a shape. I added microusb hole for charging and a wall for a Lithium polymer battery. Also, there is a hole for the button, I use this button to inform the system if the amount of water is changed.

{{< image src="/images/3d-model.png" alt="3D model" position="center" style="width: 100%;" >}}

## Improvement

Now I have a working system, but it can be improved by adding a few sensors. An accelerometer will help to reduce measurement mistakes, for our system, it's important to keep the bottle vertically. Adding LED helps users to understand if the system works in a proper way or not. To automate the process of the state system we can use the reed switch.

## Conclusion

As a result, I have a working system that informs me about water consumption per day. It can be improved by adding more sensors.

{{< image src="/images/screens.png" alt="Prototype" position="center" style="width: 71.5%;" >}}

{{< image src="/images/prototype.png" alt="Prototype" position="center" style="width: 70%;" >}}
