---
title: "Smart Water Bottle"
date: "2023-04-21"
summary: "How I made a smart bottle (from scratch!) that tracks water consumption by designing and building hardware and software systems end-to-end"
tags:
  - projects
  - hardware
  - react native
  - googlecloud
  - fusion360
  - 3d printing
---

{{< youtube Yq9PTi10ka4 >}}

As a software developer, I often found myself immersed in long coding sessions, leading to inadequate water consumption. This lack of hydration affected my health and productivity. To address this, I embarked on a journey to create the "Smart Bottle," a device designed to track and remind me of my daily water intake.

## Concept and Design

The key challenge was designing an unobtrusive yet effective monitoring system. The device needed to be compact, integrate seamlessly with a water bottle, and provide real-time data to the user. I decided on a multi-platform approach, leveraging a React Native app to ensure accessibility across various devices.

### Choosing the Right Sensor

After researching various sensors, I settled on the VL53L0X Time-of-Flight ranging sensor. This sensor stood out due to its precision, compact size, and waterproof characteristics, making it ideal for measuring the water level inside the bottle without direct contact with the liquid.

## Technical Implementation

### ESP32 Microcontroller

I chose the ESP32 microcontroller for its versatility and Wi-Fi capabilities. It serves as the central processing unit, collecting data from the sensor, performing necessary calculations, and communicating with the external server.

### Data Processing and Transmission

The core functionality revolves around accurately measuring and transmitting water level data. The VL53L0X sensor measures the distance to the water surface, and these readings are converted to volume measurements using the cylinder volume formula. This data is then sent to the server for storage and analysis.

## Server and Database

I deployed a Java/Spring Boot server on Google Cloud for its reliability and scalability. The server handles incoming data, processes it, and stores it in a structured database.

### Database Structure

The database is designed with three key fields: `amount`, `date`, and `time`. This structure allows for detailed tracking and analysis of water consumption patterns over different periods.

## Physical Design and Modeling

In designing the physical aspect of the device, I used Fusion 360 to create a practical and aesthetic design. The design includes a rechargeable battery compartment, a micro-USB port for easy charging, and a user-friendly interface with a button to manually update water levels.

[![Model](/images/model.png)](/images/model.png)

## Improvement and Future Plans

To enhance accuracy and user experience, I plan to integrate an accelerometer to ensure the bottle is in the correct position during measurements. LED indicators will provide visual feedback on the device's status, and a reed switch will automate the system's operational state.

## Conclusion

The "Smart Bottle" project has successfully demonstrated the feasibility of using technology to improve everyday health practices. It stands as a testament to the potential of integrating hardware and software to create practical, user-friendly solutions.

[![Screen](/images/screens.png)](/images/screens.png)
