---
title: "IoT-Based Indoor Environment Monitoring Device"
category: "Academic"
date: 2023-06-01
excerpt: "Designed an IoT system using Azure and Power Automate to monitor air quality and temperature in real time."
header:
  overlay_image: /assets/images/EnvMonitoring.png
  overlay_filter: 0.3
tags: [IoT, Azure, Sensors, Power Automate]
layout: single
author_profile: true
read_time: true
share: true
related: true
---



# **Indoor Environment Monitoring System**

This project implements a smart IoT-based system designed to monitor and improve indoor environmental conditions. It uses low-cost hardware connected to Azure cloud services to deliver real-time monitoring, automated environmental control, anomaly detection, and temperature forecasting.



## **Project Overview**

* Monitors temperature, humidity, CO, LPG, and smoke in real time.
* Automatically controls a humidity diffuser based on sensor readings.
* Detects abnormal gas levels and issues alerts to the user.
* Generates temperature predictions for the next 24 hours using historical data.
* Integrates hardware, cloud analytics, and dashboard visualizations into a single end-to-end solution.



## **Hardware Components**

* ESP32 microcontroller for data processing and communication
* DHT22 temperature and humidity sensor
* MQ2 gas sensor calibrated for CO, LPG, and smoke detection
* Relay module and diffuser for automated humidity control
* Fully calibrated MQ2 sensor to ensure accurate clean-air reference values


## **Cloud Architecture**

* Azure IoT Hub receives data from the ESP32 device.
* Azure Stream Analytics processes and routes the telemetry to multiple destinations:

  * Power BI for real-time dashboards
  * Cosmos DB for historical data storage
  * Blob Storage for reference statistics
  * Service Bus Queue for automated alert workflows
* Power Automate listens to the queue and sends notifications to the user when high gas levels are detected.


{% include figure 
   image_path="/assets/images/img26.jpg" 
   alt="System Architecture Diagram" 
   caption="Figure 1: High-level architecture of the IEM Device."
%}



## **Key Features**

### **Real-Time Monitoring**

* Live sensor data for gas, temperature, and humidity.
* Power BI dashboard displays up-to-date readings and alerts.
* Alerts appear directly on the dashboard for quick interpretation.

{% include figure 
   image_path="/assets/images/img43.jpg" 
   alt="Dashboard for outlier detection" 
   caption="Figure 2: Detected Statistical Outliers."
%}


### **Automated Humidity Control**

* ESP32 calculates hourly average humidity.
* If the average drops below 40%, the relay activates the diffuser automatically.
* The diffuser can also be turned off manually through the dashboard.

### **Gas Leak Detection**

* Uses a statistical method based on mean and standard deviation to identify anomalies.
* Detects sudden spikes in CO, LPG, or smoke readings.
* Sends an automated alert through Power Automate and Telegram.


## **Predictive Analytics**

* Azure Function App runs a regression model using historical temperature data stored in Cosmos DB.
* Predicts temperature trends for the next 24 hours.
* Predicted values are stored in Blob Storage and displayed in Power BI.
* The combined view of historical and predicted data helps users anticipate changes in indoor conditions.


## **Outcome**

This project results in a complete IoT-to-cloud pipeline that combines real-time sensing, automated decision-making, historical analysis, and predictive intelligence. Improved real-time safety monitoring in indoor environments through cloud-connected automation.


