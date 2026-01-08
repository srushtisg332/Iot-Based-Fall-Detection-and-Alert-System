# IoT-Based Fall Detection and Alert System

## Abstract
Falls are a major cause of injury among elderly individuals and patients living independently. Delayed medical attention after a fall can lead to serious health complications. This project presents an IoT-based fall detection and alert system using the ESP32 microcontroller and the MPU6050 accelerometer–gyroscope sensor. The system continuously monitors human motion, detects abnormal acceleration patterns associated with falls, and sends real-time alert notifications over Wi-Fi using the Blynk IoT platform.

## Project Description
The IoT-Based Fall Detection and Alert System is an embedded healthcare monitoring solution designed to automatically detect fall incidents without user interaction. The ESP32 collects real-time motion data from the MPU6050 sensor, processes acceleration and angular velocity values using Embedded C logic, and identifies fall conditions based on predefined threshold limits. When a fall is detected, the system immediately triggers an alert event through the Blynk cloud platform, enabling quick response and improved safety.

## System Architecture
1. MPU6050 sensor measures acceleration and angular velocity along X, Y, and Z axes  
2. ESP32 reads and processes sensor data in real time  
3. Motion interrupt and threshold logic identify fall conditions  
4. Wi-Fi communication module sends alert notifications  
5. Blynk cloud platform logs events and displays sensor data  

## Hardware Requirements
- ESP32 Development Board  
- MPU6050 Accelerometer and Gyroscope Module  
- Jumper Wires  
- USB or External Power Supply  
- Active Wi-Fi Network  

## Software Requirements
- Arduino IDE  
- Embedded C  
- ESP32 Board Support Package  
- Adafruit MPU6050 Sensor Library  
- Blynk IoT Library  

## Functional Workflow
- Power on the ESP32 and initialize serial communication  
- Establish Wi-Fi connection and authenticate with Blynk server  
- Initialize MPU6050 sensor and configure motion detection interrupts  
- Continuously monitor acceleration and gyroscope values  
- Compare sensor readings against calibrated threshold values  
- Detect fall events based on abnormal motion patterns  
- Trigger real-time alerts and log events on the Blynk dashboard  
- Display live sensor values for remote monitoring  

## Algorithm Overview
1. Read acceleration data from MPU6050  
2. Calculate sudden change in acceleration magnitude  
3. Compare acceleration values with predefined threshold  
4. If threshold exceeds the limit, classify as fall event  
5. Generate alert notification and send data to cloud  

## Key Features
- Automatic fall detection using motion and tilt sensing  
- Real-time IoT-based alert notification  
- Continuous monitoring of acceleration and gyroscope data  
- Cloud-based visualization and event logging  
- Scalable and low-cost embedded design  

## Applications
- Elderly care and assisted living environments  
- Patient monitoring in hospitals and home care  
- Smart healthcare and wearable safety devices  
- Emergency alert and monitoring systems  

## Limitations
- Threshold-based approach may produce false positives during rapid movements  
- Continuous internet connectivity is required for alerts  
- Battery optimization is required for wearable deployment  

## Future Scope
- Integration of GPS module for location tracking  
- GSM-based SMS and voice call alert system  
- Machine learning algorithms for accurate fall classification  
- Mobile application for enhanced user interaction  
- Power-efficient design for long-term wearable usage  

## Learning Outcomes
- Hands-on experience with ESP32 microcontroller  
- Real-time sensor interfacing and data acquisition  
- Embedded C programming for motion-based event detection  
- IoT cloud communication using Blynk  
- Debugging and testing embedded hardware systems  


## CODE
     B: SOURCE CODE
     #define BLYNK_TEMPLATE_ID "TMPL3vyg4Zcw5"
     #define BLYNK_TEMPLATE_NAME "Iot based fall detector"
     #define BLYNK_AUTH_TOKEN "9vgSFiLullfiTzHxdFeyRDhKWb6Rdb7m"   
     #define BLYNK_PRINT Serial
     #include <WiFi.h>
     #include <WiFiClient.h>
     #include <BlynkSimpleEsp32.h>
     #include <Adafruit_MPU6050.h>
     #include <Adafruit_Sensor.h>
     #include <Wire.h>
     Adafruit_MPU6050 mpu;
     char auth[] = BLYNK_AUTH_TOKEN;
     char ssid[] = "sindhu";
     char pass[] = "sindhu193";
     BlynkTimer timer;
     void sendSensor()
     {
     if(mpu.getMotionInterruptStatus()) {
    /* Get new sensor events with the readings */
    sensors_event_t a, g, temp;
    mpu.getEvent(&a, &g, &temp);
         /* Print out the values */
    Serial.print("AccelX:");
    Serial.print(a.acceleration.x);
    Serial.print(",");
    Serial.print("AccelY:");
    Serial.print(a.acceleration.y);
    Serial.print(",");
    Serial.print("AccelZ:");
    Serial.print(a.acceleration.z);
    Serial.print(", ");
    Serial.print("GyroX:");
    Serial.print(g.gyro.x);
    Serial.print(",");
    Serial.print("GyroY:");
    Serial.print(g.gyro.y);
    Serial.print(",");
    Serial.print("GyroZ:");
    Serial.print(g.gyro.z);
    Serial.println("");
    // You can send any value at any time.
    // Please don't send more that 10 values per second.
        if( a.acceleration.x >=5.0 )
    {
                  Blynk.logEvent("alert","Fall detected");
    }
    Blynk.virtualWrite(V0, a.acceleration.x);
    Blynk.virtualWrite(V1, a.acceleration.y);
    Blynk.virtualWrite(V2, a.acceleration.z);
    Blynk.virtualWrite(V3,g.gyro.x);
    Blynk.virtualWrite(V4,g.gyro.y);
    Blynk.virtualWrite(V5,g.gyro.z);
    delay(200);
          }
         }
        void setup()
         {   
          Serial.begin(115200);
              Blynk.begin(auth, ssid, pass);
               Blynk.logEvent("alert","Fall detected !");
           while (!Serial)
           delay(10); // will pause Zero, Leonardo, etc until serial console opens
            Serial.println("Adafruit MPU6050 test!");
        // Try to initialize!
         if (!mpu.begin()) {
           Serial.println("Failed to find MPU6050 chip");
           while (1) {
             delay(10);
           }
         }
         Serial.println("MPU6050 Found!");
       //setupt motion detection
         mpu.setHighPassFilter(MPU6050_HIGHPASS_0_63_HZ);
         mpu.setMotionDetectionThreshold(1);
         mpu.setMotionDetectionDuration(20);
         mpu.setInterruptPinLatch(true); // Keep it latched.  Will turn off when reinitialized.
         mpu.setInterruptPinPolarity(true);
         mpu.setMotionInterrupt(true);
         Serial.println("");
         delay(100);
         timer.setInterval(1000L, sendSensor);
       }
       void loop()
       {
         if(Blynk.connected())
         {
           Serial.println("Device is online");
         }
             else
             {
               Serial.println("Device is offline")
         }
             Blynk.run();
             timer.run();
        }
## Conclusion
The IoT-Based Fall Detection and Alert System effectively demonstrates the integration of embedded systems and IoT technologies for healthcare safety applications. By combining the ESP32 microcontroller with the MPU6050 motion sensor, the system reliably detects fall events and transmits real-time alerts via Wi-Fi using the Blynk platform. The project addresses practical challenges such as sensor calibration, false detection reduction, and reliable communication, highlighting the potential of low-cost IoT solutions in emergency response and patient monitoring systems.
